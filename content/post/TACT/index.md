+++
title = "How to built a completely resolved species-level phylogeny using TCAT"
subtitle = "Using Vitales as example"

date = 2020-09-06T00:00:00
# lastmod = 2020-09-06T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Miao Sun"]

tags = ["Macroevolution", "Incomplte sampling", "Diversification", "Phylogeny", "Taxonomy", "Vitales"]

summary = "A completely resolved species-level phylogeny is necessary for studies in macroevolution and macroecology. Sampling fraction and taxonomy are the two most commonly used methods to account for incomplete sampling, however, the pros and cons for both methods are unappreciated. Here I biefly demonstrate superior method --- TACT, using dated Vitales tree and its taxonomy to generate a completely resolved species-level Vitales tree."

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["tree_of_life", "PhyloSynth"]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder. 
[image]
  # Caption (optional)
  caption = "TACT"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Smart"

  # Show image only in page previews?
  preview_only = false

+++

Phylogenetic tree is a crucial reference system for biological research. It carries the key information for species diversification through geological time. A phylogeny tree with all members of a lineage presented is necessary for questions in macroecology and macroevolution. Hence, there are many comparative biological methods (e.g., diversification) integreated "sampling fractions" or other approches to correct sparsely sampled phylogenies of higher taxonomic groups. For example, "sampling fractions" in RPANDA (Morlon et al. 2016) and "global sampling fraction" and "clade-specific sampling fraction" in BAMM (Rabosky, 2014; Sun et al., 2020). However, this approach could lead to low statistical power to infer rate variation, or spurious patterns of diversification, which may further impact the evolutionary implication (Chang et al., 2019).   
A new stochastic polytomy resolution method --- Taxonomic Addition for Complete Trees (TACT) is able to avoid the biases arised above, and also can greatly improve the power of estimation in diversification analyses (Chang et al., 2019). It uses birth–death-sampling model and taxonomic information to place unsampled taxa onto the ultrametric target backbone phylogeny. It produces a pseduposterior distribution of phylogenies, integrating diversification rates with phylogenetic uncertainty and speciation times, while this approach also can be labor and computation time intense as well as require topology constraint. Besides TACT, other stochastic polytomy resolvers, including PASTIS (Thomas et al., 2013) and CorSiM (Cusimano et al., 2012), but TACT performes better (See Chang et al., 2019).  

_The caveats which researchers should pay attention is that complete phylogenies generared by stochastic polytomy resolvers should NOT be used for estimating the trait evolution. Because the way of how complete tree is generated, the true rate of trait evolution can be overestimated, and the phylogenetic signal is lost._  

The Author [Jonathan Chang](https://jonathanchang.org/) already has a detailed [tutorial](https://github.com/jonchang/tact) of how to install and run TACT. Here I only focus on how to prepare a taxonomy list and built up pipeline for my own project.  

## General steps  
First thing first, you need two files to run TACT (assuming that you have successfully installed TACT and its dependencies):  
1. An ultrametric backbone phylogeny (e.g., _Vitales\_dated.tre_)   
2. A taxonomy of target clade  

+ this can be taxonomy tree (see Chang et al., 2019)  
+ can also be a taxonomic list in csv format with all taxonomic ranks listed (e.g., genus, family, order; here as _Vitales.csv_)  
  
For the case in my post, I use [Vitales](https://en.wikipedia.org/wiki/Vitaceae) as an example.  The Vitales taxonomic list is generated from [WCSP website](http://www.plantsoftheworldonline.org), and the higher taxonomic ranks are queried from [APWeb](http://www.mobot.org/MOBOT/research/APweb/). The backbone tree was built using RAxML with molecular data mined from GenBank via [PyPHLAWD](https://github.com/FePhyFoFum/PyPHLAWD), and then dated using [treePL](https://github.com/blackrim/treePL).    
  
### 1. Prepare a taxonomic list
  - A csv format  
  - All taxonomic ranks referenced from [APWeb](http://www.mobot.org/MOBOT/research/APweb/)  
  - Columns layout as `order,family,genus,genus.species`  

```{r echo=TRUE}  
# devtools::install_github("wcornwell/taxonlookup")

rm(list=ls())
library("taxonlookup")
library("ape")
library("dplyr", warn.conflicts = FALSE, quietly = TRUE)

#read in the complete taxonomic list

Vitales <- read.csv("Vitales_rawlist.csv", header = T, stringsAsFactors = F)

# > head(Vitales)
# [1] "Acareosperma spireanum"             
# [2] "Ampelocissus abyssinica"            
# [3] "Ampelocissus acapulcensis"          
# [4] "Ampelocissus acetosa"               
# [5] "Ampelocissus aculeata"              
# [6] "Ampelocissus africana var. migeodii"

# lookup APGWeb based on species names  
 Vitales.apg <- lookup_table(unique(Vitales), missing_action = "NA", by_species=TRUE)  

 #just manual check if there are some genera are failed to have query  
 if(sum(is.na(Vitales.apg$family)) >0){
   tt <- Vitales.apg[is.na(Vitales.apg$family),]
   print(tt)
 }

# rearrange columns as taxonomic ranks
APG_table <- Vitales.apg %>% mutate(genus.species=row.names(Vitales.apg)) %>% select(order, family, genus, genus.species)

#
#> head(APG_table)
#order   family        genus                       genus.species
#1 Vitales Vitaceae Acareosperma              Acareosperma spireanum
#2 Vitales Vitaceae Ampelocissus             Ampelocissus abyssinica
#3 Vitales Vitaceae Ampelocissus           Ampelocissus acapulcensis
#4 Vitales Vitaceae Ampelocissus                Ampelocissus acetosa
#5 Vitales Vitaceae Ampelocissus               Ampelocissus aculeata
#6 Vitales Vitaceae Ampelocissus Ampelocissus africana var. migeodii

#replace space as underscore
APG_table$genus.species <- gsub(" ", "_",APG_table$genus.species)

#save the apg lookup table, and prepare for next step TACT  
write.csv(APG_table, "Vitales.csv", row.names=FALSE, quote=FALSE)

```
  
### 2. Run TACT

  ```{bash}
#Build a taxonomic tree  
	tact_build_taxonomic_tree Vitales.csv --output Vitales.taxonomy.tre  

# this step adove can be skipped, if you already have a taxonomy tree  

#Run the TACT stochastic polytomy resolver algorithm in conjunction with the backbone phylogeny  
	tact_add_taxa --backbone Vitales_dated.tre --taxonomy Vitales.taxonomy.tre --output Vitales.tacted --verbose  
	
#check the TACT results  
	tact_check_results Vitales.tacted.newick.tre --backbone Vitales_dated.tre --taxonomy Vitales.taxonomy.tre > Vitales_checkresults.csv  

```
    
### 3. Visualize and compare trees

```{r echo=TRUE}  
library("ape")
tree1 <- ladderize(read.tree("./Vitales_dated.tre"))

plot.phylo(tree1, cex=0.1, type = "fan", main="Backbone tree with NCBI data")

```
This is the original backbone tree before TACT treatment  
{{< figure src="Comparison_tree_before_after_TACT1.1.jpg" title="Vitales backbone" numbered="true" >}}

Now we show which tips on the complete species-level tree are added by TACT:  
```{r echo=TRUE} 
tree2 <- ladderize(read.tree("./Vitales.tacted.newick.tre"))
tip.cl <- ifelse(tree2$tip.label %in% tree1$tip.label, "black", "red")
plot.phylo(tree2, tip.color=tip.cl, cex=0.1, type = "fan", main="complete tree with NCBI + Taxonomic data")
mtext("Red tips are species added by TACT", col = "red", side=1, line=-5, outer=TRUE)
```
{{< figure src="Comparison_tree_before_after_TACT1.2.jpg" title="Vitales Tacted Tree1" numbered="true" >}}


Last, We highlight which tree edges on the complete species-level tree are added by TACT:  

```{r echo=TRUE} 
library("phytools")
x <- setNames(ifelse(tree2$tip.label %in% tree1$tip.label, "0", "1"), tree2$tip.label)

a <- names(x)[x=="0"]
b <- names(x)[x=="1"]

tt<-paintBranches(tree2,edge=sapply(b,match,tree2$tip.label),
                  state="b",anc.state="a")
cols<-setNames(c("black","red"),c("a","b"))

plot(tt, colors=cols, fsize=0.1, type="fan", lwd=0.5)
mtext("Red branches are imputated by TACT", col = "red", side=3, line=-1.5, outer=TRUE)
```

{{< figure src="Comparison_tree_before_after_TACT2.jpg" title="Vitales Tacted Tree2" numbered="true" >}}

That's it.

### Reference

1. Thomas G.H. et al. PASTIS: an R package to facilitate phylogenetic assembly with soft taxonomic inferences. Methods Ecol. Evol. 4:1011–1017 (2013).  
1. Chang et al. Estimating Diversification Rates on Incompletely Sampled Phylogenies: Theoretical Concerns and Practical Solutions. Syst. Biol. 69(3):602–611, 2020  
1. Cusimano N.et al. A new method for handling missing species in diversification analysis applicable to randomly or nonrandomly sampled phylogenies. Syst. Biol. 61:785–792 (2012). 
1. Morlon, H. et al. RPANDA: an R package for macroevolutionary analyses on phylogenetic trees. Methods Ecol. Evol. 7, 589–597 (2016).  
1. Rabosky, D. L. Automatic detection of key innovations, rate shifts, and
diversity-dependence on phylogenetic trees. PLoS ONE 9, e89543 (2014).  
1. Revell, J. L. Phytools: an R package for phylogenetic comparative biology (and other things). Methods Ecol. Evol. 3, 217–223 (2012).  
1. Sun, M. et al. Estimating rates and patterns of diversification with incomplete sampling: A case study in the rosids. Am. J. Bot. 107, 1–15 (2020).  
