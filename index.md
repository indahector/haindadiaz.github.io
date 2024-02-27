@def title = "Héctor Alejandro Inda Díaz"

<!-- -----------------
     BIOGRAPHY SECTION
     ----------------- -->

\begin{section}{name="about"}

<!-- RIGHT COLUMN -->
@@col-12,col-lg-4,profile

\img{"/assets/img/Hector_Inda_Diaz_1.jpg", class="avatar avatar-square", alt="Héctor Alejandro Inda Díaz"}
\portrait{
  name="Héctor Alejandro Inda Díaz",
  job="Associate Researcher",
  link="https://www.eaglerockanalytics.com/",
  linkname="Eagle Rock Analytics",
  resume="/assets/Resume_IndaDiaz.pdf",
  cv="/assets/CV_IndaDiaz.pdf",
  email="indahector@gmail.com",
  gscholar="https://scholar.google.com/citations?user=MBdNFm0AAAAJ&hl=es&oi=sra",
  github="https://github.com/indahector",
  linkedin="https://www.linkedin.com/in/indahector"
}
@@ <!-- end of column -->


<!-- LEFT COLUMN -->
@@col-12,col-lg-8

\begin{biography}{}
 "I’m a Research Associate at [Eagle Rock Analytics](https://www.eaglerockanalytics.com/). Among other things I am interested in the climate change, big data analysis, [North American Monsoon](https://www.climate.gov/news-features/blogs/enso/north-american-monsoon), [Atmospheric Rivers](https://www.noaa.gov/stories/what-are-atmospheric-rivers), atmospheric extreme events (extreme precipitation, atmospheric rivers, extratropical cyclones, heat waves, tropical cyclones, etc.), atmospheric and ocean numerical modeling, supercomputing, software development, open science and open software."
\end{biography}

\shortcv{
  academic_interests=["Atmospheric Science", "Physical Oceanography", "Climate", "Fluid Dynamics", "Supercomputing", "Free software", "Python", "Numerical Modeling"],
  education=[
    ("Data science certification, 2023", "The Data Incubator"),
    ("Ph.D. in Atmospheric Science, 2022", "University of California, Davis"),
    ("M.S. in Physical Oceanography, 2015", "Ensenada Center for Scientific Research and Higher Education"),
    ("B.Sc. in Physics, 2012", "Universidad Nacional Autónoma de México")],
  other_interests=["Volleyball", "Snowboarding", "Specialty coffee"]
}

@@ <!-- end of column -->



\end{section}

\begin{section}{name="recent news"}

<!-- --------------
     SHORT PUB LIST SECTION
     -------------- -->


@@col-12,col-lg-6,pubs
 \
\sectionheading{"Recent publications", class="col-md-12"}
{{pub}}

@@


<!-- --------------
     NEWS SECTION
     -------------- -->


@@col-12,col-lg-6,news

 \
[//]: <> \sectionheading{"News", class="col-md-12"}
\sectionheading{"Recent news", class="col-md-12"}
{{recentnews 3}}

@@
\end{section}



<!-- --------------
     SKILLS SECTION
     -------------- -->

\begin{section}{name="skills", class="wg-featurette", rowclass="featurette"}
[//]: <> 
\sectionheading{"Favorite tools", class="col-md-12"}
[//]: <> 
\skill{"Python", "", img="/assets/img/Python.png"}
\skill{"Vim", "", img="/assets/img/Vim.png"}
\skill{"Jupyter", "", img="/assets/img/Jupyter.png"}
\skill{"Unix", "", img="/assets/img/Unix.jpg"}
\skillhr{"TECA", "https://teca.readthedocs.io/en/latest/", img="/assets/img/TECA.png"}
\skill{"dask", "", img="/assets/img/dask.png"}
[//]: <> 
\end{section}


