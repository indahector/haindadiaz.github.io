@def title = "Argel Ramírez Reyes"

<!-- -----------------
     BIOGRAPHY SECTION
     ----------------- -->

\begin{section}{name="about"}

<!-- RIGHT COLUMN -->
@@col-12,col-lg-4,profile

\img{"/assets/img/Hector_Inda_Diaz_2.jpg", class="avatar avatar-square", alt="Héctor Alejandro Inda Díaz"}
\portrait{
  name="Héctor Alejandro Inda Díaz",
  job="PhD in Atmospheric Science",
  link="[https://cascade.lbl.gov)](https://cascade.lbl.gov/team/)",
  linkname="CASCADE - LBNL",
  resume="/assets/ARR_resume.pdf",
  email="indahector@gmail.com",
<!--   twitter="https://twitter.com/aramirezreyes", -->
  gscholar="https://scholar.google.com/citations?user=JkcQycYAAAAJ&hl=es](https://scholar.google.com/citations?user=MBdNFm0AAAAJ&hl=en&oi=sra",
  github="[https://github.com/aramirezreyes](https://github.com/indahector)",
  linkedin="[https://www.linkedin.com/in/argelramirezreyes/](https://www.linkedin.com/in/indahector/)"
}
@@ <!-- end of column -->


<!-- LEFT COLUMN -->
@@col-12,col-lg-8

\begin{biography}{}
 I’m a PhD Candidate in the Atmospheric Sciences program at UC Davis, at [Dr Da Yang’s Climate group](https://www.yang-climate-group.org/). Among other things I am interested in tropical cyclones (my current research focus), supercomputing, open science and open software. I am working towards understanding the essential elements in the genesis of tropical cyclones. My PhD Studies are mainly funded by the People of México through a CONACYT – UCMexus fellowship.
 
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
\skill{"Emacs", "50%", img="/assets/img/emacsicon.png"}
\skill{"Photography", "10%", fa="camera-retro"}

\end{section}


