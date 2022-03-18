@def title = "Argel Ramírez Reyes"

<!-- -----------------
     BIOGRAPHY SECTION
     ----------------- -->

\begin{section}{name="about"}

<!-- RIGHT COLUMN -->
@@col-12,col-lg-4,profile

\img{"/assets/img/ArgelRamirezReyes.jpg", class="avatar avatar-square", alt="Argel Ramírez Reyes"}
\portrait{
  name="Argel Ramírez Reyes",
  job="PhD Candidate in Atmospheric Science",
  link="https://www.ucdavis.edu/",
  linkname="University of California, Davis",
  resume="/assets/ARR_cv.pdf",
  email="argel.ramirez@gmail.com",
  twitter="https://twitter.com/aramirezreyes",
  gscholar="https://scholar.google.com/citations?user=JkcQycYAAAAJ&hl=es",
  github="https://github.com/aramirezreyes",
  linkedin="https://www.linkedin.com/in/argelramirezreyes/"
}
@@ <!-- end of column -->


<!-- LEFT COLUMN -->
@@col-12,col-lg-8

\begin{biography}{}
 I’m a PhD Candidate in the Atmospheric Sciences program at UC Davis, at [Dr Da Yang’s Climate group](https://www.yang-climate-group.org/). Among other things I am interested in tropical cyclones (my current research focus) in our changing climabte, tropical dynamics and open software. I am working towards understanding the essential elements in the genesis of tropical cyclones. My PhD Studies are mainly funded by the People of México through a CONACYT – UCMexus fellowship.
 
\end{biography}

\shortcv{
  interests=["Fluid Dynamics", "Tropical cyclones", "Free software", "Supercomputing", "Climate"],
  education=[
    ("PhD in Atmospheric Science, in progress.", "University of California, Davis"),
    ("Masters in Scientific Computing, 2017", "Université de Lille 1, Sciences et Technologies"),
    ("BSc in Physics, 2016", "Universidad Nacional Autónoma de México")]
}

@@ <!-- end of column -->



\end{section}

\begin{section}{name="recent news"}

<!-- --------------
     SHORT PUB LIST SECTION
     -------------- -->


@@col-12,col-lg-8,pubs
 \
\sectionheading{"Recent publications", class="col-md-12"}
{{pub}}

@@


<!-- --------------
     NEWS SECTION
     -------------- -->


@@col-12,col-lg-4,news

 \
\sectionheading{"News", class="col-md-12"}
{{recentnews 3}}

@@
\end{section}



<!-- --------------
     SKILLS SECTION
     -------------- -->

\begin{section}{name="skills", class="wg-featurette", rowclass="featurette"}

\sectionheading{"Favorite tools", class="col-md-12"}

\skill{"Julia", "90%", img="/assets/img/julia-dots-colors.svg"}
\skill{"Photography", "10%", fa="camera-retro"}

\end{section}


