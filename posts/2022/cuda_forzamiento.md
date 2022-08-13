+++
pubdate = Date("2022-08-12")
title = "Implementando una parameterización convectiva para la GPU usando Oceananigans.jl"
showall = true
category = "code"
tags = ["CUDA", "julia","conveccion"]
+++


En esta entrada comparto mi experiencia sobre lo fácil que es extender las capacidades de Oceananigans.jl en el lenguaje de programación Julia con la ayuda de CUDA.jl y Oceananigans.jl. Todo el código mostrado en esta entrada del blog puede encontrarse [aquí](https://github.com/aramirezreyes/RamirezReyes_ShallowWaterInFPlane/commit/8834855f91e7d76364f7e553a258ae4fc2daa08f)

![Ciclones tropicales en un modelo de aguas poco profundas con una parametrización convectiva](/assets/img/tcs_shallow_water.png)

[Oceananigans.jl](https://github.com/CliMA/Oceananigans.jl) se describe como "Un software escrito en Julia para una dinámica de fluidos rápida, amigable y flexible con sabor a océano en CPUs y GPUs. Se ha desarrollado como parte de la [Climate Modelling Alliance](https://clima.caltech.edu). Realmente cumple lo que promete, ya que ejecutar una de las simulaciones de ejemplo en las GPU (unidades de procesamiento gráfico) es tan fácil como cambiar una línea de un script para que diga "GPU" en lugar de "CPU".

Una de mis cosas favoritas del lenguaje julia es que la distancia entre un "usuario" y un "desarrollador" es mucho menor que en otros lenguajes de alto nivel. Esto significa que si eres capaz de escribir un script para ejecutar una simulación utilizando el paquete de otra persona, tienes casi todas las habilidades necesarias para escribir un código rápido que funcione tan bien como escribir código en el lenguaje C (el estándar de oro) y que se parezca a la implementación de la mayoría de las funciones disponibles en el lenguaje. 

Aquí muestro cómo trabajar con Oceananigans.jl es una experiencia fantástica, y que extender sus capacidades para trabajar en una GPU es casi tan fácil como hacerlo para la CPU.

Lo que quiero hacer es extender el modelo de aguas someras disponible en Oceananigans, e implementar una representación de la convección (el modelo de aguas someras puede representar nuestra atmósfera y la convección representa tormentas). Si no te interesan los detalles de este modelo, puedes saltarte la siguiente sección e ir directamente a la Implementación.


## El modelo

El modelo que estamos implementando es el de [Yang 2013](https://arxiv.org/abs/1210.5533). El conjunto completo de ecuaciones que resolveremos es:

\begin{align}\label{SWSystem}
  \frac{\partial u}{\partial t} + u\frac{\partial u}{\partial x} + v\frac{\partial u}{\partial y} -fv & = -g \frac{\partial h}{\partial x} -\frac{u}{\tau_d} \\
  \frac{\partial v}{\partial t} + u\frac{\partial v}{\partial x} + v\frac{\partial v}{\partial y} + fu & = -g \frac{\partial h}{\partial y} -\frac{v}{\tau_d} \\
  \frac{\partial h}{\partial t} + \nabla \cdot (\mathbf{u}h) & = Q(x,y,t) -\frac{h - h_0}{\tau_d}  + F_{rs},
\end{align}

Se trata de las conocidas [ecuaciones de aguas someras](https://en.wikipedia.org/wiki/Shallow_water_equations) que ya están resueltas por el modelo ShallowWaterModel en Oceananigans.jl. Añadimos algo de fricción en todos los campos para ser consistentes con otros estudios, y un forzamiento al campo de altura (h). Esta es nuestra parametrización convectiva que añade "masa" al campo h de la siguiente manera:

\begin{align}
  Q(x,y,t) & = \begin{cases}
    -\frac{q}{r_c \tau_c} \left [ 1 - \left ( \frac{\Delta t - \tau_c/2}{\tau_c/2} \right )^2 \right ] \left ( 1 - \frac{r^2}{r_c^2} \right ) & \textrm{when a convective event is active}\\
    0,& \textrm{otherwise},
    \end{cases}
\end{align}

donde $q$, $t_c$ y $r_c$ son la magnitud, el radio y la duración de los eventos convectivos, $\Delta t$ es el tiempo transcurrido desde el inicio del evento convectivo y $r$ es la distancia desde el punto en que se desencadenó un evento convectivo. Cada evento convectivo se desencadena cuando la masa del sistema supera cierto umbral, lo que representa un sumidero de masa. Esta parametrización es interesante porque puede reproducir algunas formas de convección organizada como la [oscilación de Madden-Julian](https://en.wikipedia.org/wiki/Madden%E2%80%93Julian_oscillation) que afecta al tiempo meteorológico en buena parte del globo y que sigue desconcertando a los científicos. Nosotros queremos utilizarlo para estudiar cómo se forman e intensifican los huracanes.


## La implementación

### Intento de implementación 1
Vamos a pensar en un solo punto de la red. Nuestra hoja de ruta es:

1. Comprobar si este punto (i,j) cumple las condiciones para inyectar masa.
1. Si es convectivo, recorre sus alrededores, añadiendo masa a cada vecino.

### La programación paralela requiere pensar las cosas de manera diferente (o evitar una condición de carrera)

Aunque la hoja de ruta anterior parece razonable, tiene una limitación: no se puede paralelizar fácilmente. Imagina un punto (A) que cae entre dos eventos convectivos (C1 y C2). La estrategia de paralelización consistiría en que cada unidad de procesamiento diferente (podría ser cada núcleo de una CPU/GPU o cada tarea) se encargaría de un evento convectivo. Al añadir masa, cada evento convectivo leerá el valor original almacenado en A y añadirá su propia parte de masa. Esto puede ocurrir de dos maneras:

1. C1 lee el valor inicial de A y le añade masa. 2. C2 lee el valor actualizado y le añade masa. Éxito.
1. C1 y C2 leen el valor original de A y ambos le añaden masa. Luego se actualizan con su propio valor nuevo al mismo tiempo. ¡Error! El valor final no contiene la masa de ambos eventos convectivos, sino sólo del último que escribió su valor actualizado. **Esto se llama una condición de carrera**. 

El problema se agrava si tenemos más de dos eventos convectivos añadiendo masa al mismo tiempo. Tenemos que repensar la estrategia.

### Intento de implementación 2

Si esperamos paralelizar el problema, necesitamos otra estrategia. He aquí una segunda propuesta. 

Pensemos de nuevo en un punto (i,j) de la malla.

1. Recorrer sus alrededores.
1. 2. Si algún punto vecino es convectivo, añade masa a (i,j).

La diferencia es sutil, pero si paralelizamos la segunda propuesta, no existe ninguna condición de carrera, ya que cada punto de la matriz "masa" sólo sería escrito por una unidad de procesamiento en cada paso de tiempo. Nos quedaremos con ésta. Este problema puede llamarse "embarazosamente paralelo" porque cada unidad de procesamiento puede hacer su propia tarea sin preocuparse de intercambiar información con las demás. Esto es algo en lo que las GPUs son muy buenas y esta característica motiva el resto del trabajo.

### Implementación en la CPU

Para implementar nuestra parametrización convectiva, necesitaremos dos arrays auxiliares. Uno almacenará una condición booleana para indicar si cada punto es convectivo. El otro almacenará el momento en que cada punto comenzó a convectar la última vez. Vamos a llamarlos "isconvecting" y "convection_triggered_time". Estos dos tendrán el mismo tamaño que el campo `h` y otros campos del dominio. Ahora, necesitamos una función que, en cada paso de tiempo, evalúe las condiciones en cada punto del dominio y decida es la parametrización convectiva debe actuar. Ahora queremos que en cada paso de tiempo, estas dos matrices se actualicen con la información actual sobre los puntos que deben ser convectivos.

```plaintext
Para cada punto del dominio
	Calcula el tiempo que ha estado convectando
	¿Ha estado este punto convectando menos de lo previsto? o
	¿Está este punto por debajo del umbral de convección? o
	¿Es este punto un nuevo punto de convección?
	Si alguna de estas tres condiciones es cierta
		actualiza una matriz para indicar que este punto está convectando e indica cuándo empezó
	fin
fin
```

Que en Julia se ve:

```julia
function update_convective_events_cpu!(isconvecting,convection_triggered_time,h,t,τ_convec,h_threshold)
	for ind in eachindex(h)
		time_convecting = t - convection_triggered_time[ind]
		needs_to_convect_by_time = isconvecting[ind] * (time_convecting < τ_convec) #ha sido convectivo por poco timepo?
		needs_to_convect_by_height = h[ind] >= h_threshold
		will_start_convecting = needs_to_convect_by_height * iszero(needs_to_convect_by_time) #necesitamos actualizar el timepo?
		needs_to_convect = needs_to_convect_by_time | needs_to_convect_by_height
		isconvecting[ind] = needs_to_convect
		convection_triggered_time[ind] = ifelse(will_start_convecting, t, convection_triggered_time[ind])
	end
    return nothing
end
```
Ahora, queremos que cada unidad de procesamiento añada masa a un solo punto. ¿Cuánta masa? Depende de cuántos puntos convectivos haya a su alrededor, de la distancia a la que estén y del tiempo que lleve cada uno convectivo. 

Algo así como:

```plaintext
Para cada vecino
	¿Este vecino es convectivo? entonces
		¿A qué distancia está?
		¿Durante cuánto tiempo ha estado convectando?
		add_mass_to_myself(distancia, tiempo)
	end
fin	
```

En julia:

```julia
function heat_at_point(i,j,k,current_time,τc,isconvecting,convection_triggered_time,numelements_to_traverse, heating_stencil)
    forcing = 0.0
    nn = -numelements_to_traverse
    np = numelements_to_traverse
    for neigh_j in nn:np
        jinner = j + neigh_j
        for neigh_i in nn:np
            iinner = i + neigh_i
             if isconvecting[iinner , jinner]
                 forcing = forcing - heat(current_time,convection_triggered_time[iinner,jinner],τc,heating_stencil[neigh_i,neigh_j])
             end
        end
    end 
    return forcing
end
```

### Implementación para la GPU

Ahora. Aquí es donde ocurre la magia. Algo bueno de Oceananigans.jl es que tiene la mayor parte de la infraestructura lista para ayudarte a introducir algunos forzamientos en los campos del modelo. Este forzamiento se puede ejecutar en cualquier arquitectura que hayas elegido (GPU o CPU). En este caso nuestra función `heat_at_point` es lo suficientemente genérica como para que Oceananigans.jl pueda trabajar con ella.

Sin embargo, como Oceananigans.jl no gestiona nuestras matrices `isconvecting` y `convection_triggered_time` necesitamos escribir una función para actualizarlas utilizando la GPU. Afortunadamente, `CUDA.jl` es otra librería que expone la mayor parte de la funcionalidad de cuda (el conjunto de herramientas para ejecutar código de propósito general en una GPUS Nvidia) desde julia. Haciendo sólo unos mínimos cambios, nuestra nueva función tiene el siguiente aspecto:



```julia
function update_convective_events_gpu!(isconvecting,convection_triggered_time,h,t,τ_convec,h_threshold,Nx,Ny)

    index_x = (blockIdx().x - 1) * blockDim().x + threadIdx().x
    stride_x = gridDim().x * blockDim().x

    index_y = (blockIdx().y - 1) * blockDim().y + threadIdx().y
    stride_y = gridDim().y * blockDim().y

    
    for i in index_x:stride_x:Nx
        for j in index_y:stride_y:Ny
            time_convecting = t - convection_triggered_time[i,j]
            needs_to_convect_by_time = isconvecting[i,j] && (time_convecting < τ_convec) 
            needs_to_convect_by_height = h[i,j] >= h_threshold
            will_start_convecting = needs_to_convect_by_height && iszero(needs_to_convect_by_time) 
            isconvecting[i,j] = needs_to_convect_by_time || needs_to_convect_by_height 
            will_start_convecting && (convection_triggered_time[i,j] = t)
        end
    end
    
    return nothing
end
```

El único cambio significativo que hemos hecho es en la forma de iterar sobre los campos. En este caso, cada unidad de procesamiento se encargará de sólo un puñado de operaciones por lo que no necesitamos hacer un bucle sobre todos los elementos del arreglo. Este núcleo se pasará a cada unidad de procesamiento.

Esto da una velocidad considerable para ejecutar simulaciones como las mostradas al principio. 

Con julia y Oceananigans.jl, el código es tan reutilizable y versátil, que puedes dedicar más tiempo a explorar tus simulaciones que a escribirlas. Y además, ¡disfrutas de programar!

Puedes ver la implementación completa de la parametrización convectiva en [aquí](https://github.com/aramirezreyes/RamirezReyes_ShallowWaterInFPlane/commit/8834855f91e7d76364f7e553a258ae4fc2daa08f)

Referencias: 
* Yang and Ingersoll (2013) [Triggered convection, gravity waves, and the MJO: A shallow water model](https://arxiv.org/abs/1210.5533)
* The julia language (https://www.julialang.org)
* Oceananigans.jl (https://github.com/CliMA/Oceananigans.jl)
* CUDA.jl (https://github.com/JuliaGPU/CUDA.jl)
