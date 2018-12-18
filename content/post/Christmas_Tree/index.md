+++
title = "Plotting Christmas Tree"
subtitle = ":christmas_tree: The Tree of **Christmas Trees** :christmas_tree:"

date = 2018-12-17T00:00:00
lastmod = 2018-12-17T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Miao Sun"]

tags = ["Xsmas", "Open Tree", "Tree of Life", "Gymnosperm"]
summary = ""

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder. 
[image]
  # Caption (optional)
  caption = "Image credit: [**Google Image**](http://www.centralia.k12.mo.us/wordpress/wp-content/uploads/2016/12/christmas-tree-clip-art-xmas_christmas_tree_5-3333px.png)"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

  # Show image only in page previews?
  preview_only = false

# Set captions for image gallery.
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-default.png"
# caption = "Default"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-ocean.png"
# caption = "Ocean"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-forest.png"
# caption = "Forest"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-dark.png"
# caption = "Dark"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-apogee.png"
# caption = "Apogee"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-1950s.png"
# caption = "1950s"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-coffee-playfair.png"
# caption = "Coffee theme with Playfair font"
# 
# [[gallery_item]]
# album = "gallery"
# image = "theme-cupcake.png"
# caption = "Cupcake"
+++

I saw a figure of all potential Christmas tree :christmas_tree: at :bird: [Polypompholyx’s Tweet](https://twitter.com/Polypompholyx/status/1074351911581728770?s=05). 

It looks strange for my first impression. So I did a quick check from [Open Tree](https://tree.opentreeoflife.org/opentree/argus/opentree10.4@ott93302). This looks much better! 


See code below:

```{r echo=TRUE}
rm(list=ls())

# install.packages("rotl")
# install.packages("ape")

#loading libraries
library("rotl") #for querying open tree database
library("ape") #for tree manipulation

#First I preapred a table for those potential **Christmas Trees** with first column is common names,a nd second column is the scientific names
# then we read in the table as a query list to the Open Tree

query <- read.csv("XsmasTree.csv", header=TRUE, sep=",", stringsAsFactors=FALSE, quote="")
# check what our table look like this
head(query)

# query names and get ottid
species <- tnrs_match_names(names = query$Scientific_name_rept.)

# check results
head(species)
# fix fuzzy match on row 16, then updated it 
new_species<- update(species, row_number=16, new_ott_id = 248313)

# checking object
head(new_species)
species <- new_species
species.ottid <- ott_id(species)


# extracting the phylogeny summarized in the Open tree
tree <- tol_induced_subtree(ott_ids=species$ott_id, label_format = "name")

# make it looks good
tree <- ladderize(tree)
plot.phylo(tree, cex=0.8)

# rename the tree tip labels
query[[2]][16] <-  "Picea pungens" #remove one extra space
query[[2]] <- gsub(pattern="\\s", "_", query[[2]])

new_label <- paste0(tree$tip.label,"(", query[[1]][match(tree$tip.label, query[[2]])], ")")

new_tree <- tree
new_tree$tip.label <- new_label

#png("The Tree of Christams Tree.png")
#plot(new_tree, cex=0.7)
#dev.off()

#write.tree(new_tree, "renamed_Xsmas_tree.tre")

#plot a prettier tree
plot.phylo(new_tree, edge.color = "dark green", edge.width = 2, tip.color = "red", 
           label.offset = 0.5, main="The Tree of Christmas Tree", col.main="red")
mtext("---source from Open Tree", col="dark green", 3)

```
{{< figure src="Rplot02.png" title="Tree of Xsmas Trees" >}}
:snowflake: :santa: :cactus: :bell: :snowman: :evergreen_tree: