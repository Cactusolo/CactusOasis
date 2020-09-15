+++
# Project title.
title = "PhyloSynth"

# Date this page was created.
date = 2019-10-01T00:00:00

# Project summary to display on homepage.
summary = "PhyloSynth is a collaborative group of scientists that aim to provide an updated framework and set of phylogenies for the plant tree of life."

# Tags: can be used for filtering projects.
# Example: `tags = ["rosids", "diversitification"]`

tags = ["Tree of Life", "Phylogeny", "Conservation", "Macroevolution", "Seed Plant", "Biodiversity", "Macroecology"]

# Optional external URL for project (replaces project detail page).
# external_link = "https://github.com/phylosynth/Phylosandbox"

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references 
#   `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
#slides = "example-slides"

# Links (optional).
url_pdf = ""
url_slides = ""
url_video = ""
url_source = "https://phylosynth.github.io/"

# Custom links (optional).
#   Uncomment line below to enable. For multiple links, use the form `[{...}, {...}, {...}]`.
url_custom = [{icon_pack = "fab", icon="twitter", name="Follow", url = "https://twitter.com/PhyloSynth"}]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder. 
[image]
  # Caption (optional)
  caption = "Phylogeny is cool!"
  
  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Smart"
+++
  
We are marching on Phylosynth project! Some primary data, code and results will share here. Our goal is reconstructing [**a larger-scale plant Tree of Life**](https://bsapubs.onlinelibrary.wiley.com/doi/10.1002/ajb2.1041) for all [seed plants (Spermatophyta)](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=58024), using methods described in [Smith and Brown (2018)](https://bsapubs.onlinelibrary.wiley.com/doi/full/10.1002/ajb2.1019) and ideas described in [Eiserhardt et al. (2018; see below)](https://bsapubs.onlinelibrary.wiley.com/doi/pdf/10.1002/ajb2.1041), and integrating the phylogenetic backbone from  [the Plant and Fungal Trees of Life Project (PAFTOL)](https://www.kew.org/science/our-science/projects/plant-and-fungal-trees-of-life) and robust taxonomy database from [World Checklist of Selected Plant Families (WCSP)](https://wcsp.science.kew.org/home.do). We endeavor to push the boundary of the knowledge of Tree of Life, keeping this tree portable and dynamically updated, providing knowledge of the plant tree of life to science community and the public education.  

Pipeline Schema from [Eiserhardt et al. (2018)](https://bsapubs.onlinelibrary.wiley.com/doi/pdf/10.1002/ajb2.1041)

{{< figure src="work_flow.png" title="" numbered="false" >}}


### [Some key feactures](https://docs.google.com/document/d/1Hb-G9_WXhCpTpv7mpXrcS_UOH7giolAgjk9nBsRaL50):

+ **Flexible**    
  Easy for other pipelines to integrate  

+ **Dynamically updated**    
  Establish a schedule for running this pipeline at regular intervals, producing up-to-date trees. For this, we need to decide an initial frequency for generating trees. This frequency can later be adjusted based on download statistics and user feedback.  

+ **Portable for different audiences**   
  Establish one or more outlet(s) for PhyloSynth trees. This needs to take into consideration where different audiences would be looking for trees, and ensure (for scientific audiences) that there is a citable paper.   
      
+ **High quality**    
  - Build module that maps NCBI taxonomy to a widely accepted botanical taxonomy. This should in the first place be the WCSP/”names backbone” at Kew, but we need to consider the fact that other lists are in circulation.  
  - Build a module that filters NCBI data automatically according to certain rules. This could be a simple decision tree based on metadata, or a more complex machine learning approach.  
  - Build a module that evaluates resulting trees automatically using a set of statistics. This could include, among other things, monophyly statistics for higher ranks from the taxonomy used (genera and families in the case of WCSP).  
  - Establish a procedure for manual quality control by taxon experts. This would need to include a procedure for storing decisions/annotations and avoiding duplication of effort.  
  - Establish a procedure for user feedback. This would need to include a procedure for storing decisions/annotations and avoiding duplication of effort.   
