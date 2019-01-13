+++
title = "Field work activities from 2006-2012 in China"
subtitle = "Field work experiences"

date = 2019-01-13T00:00:00
# lastmod = 2018-12-17T00:00:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Miao Sun"]

tags = ["Botany", "Taxonomy", "Field Work"]
summary = ""

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["taxonomy"]

# Featured image
# To use, add an image named `featured.jpg/png` to your project's folder. 
[image]
  # Caption (optional)
  caption = "Field trip trail"

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""

  # Show image only in page previews?
  preview_only = false

+++

I have visited a lot places in south part of China. These places are genreally hostspots of biodiversity of China. _Especially I love Xizang!_ I hope I have chance to introduce more details of this facinating landscape, spelendid vegation, and each plant I came across during my trip.  

:four_leaf_clover: :cherry_blossom: :herb: :cactus: :mushroom: :palm_tree: :deciduous_tree: :blossom:

Here some bullets of my trips I have make since 2006 - 2012:  

+ 2012  
Trip to southern Yunnan along Myanmar border with Pam S. Soltis and Doug E. Soltis, collecting materials for Tree of life — China Project.
Trip to Hainan tropical rain forest with Doug E. Soltis, collecting materials of Tree for life — China Project.  
+ 2006 ~ 2011  
Joined in the expedition team of national herbarium (PE) for collection many times, successively visited a series of biodiversity hotspots, such as Jilin, Hubei, Chongqing, Guangxi, Sichuan, Xizang, etc.  

I use R code and Baidumap API to show my trips:  

Warning: This script may not work well in R markdown enviroment, but I tried by copying out the code as a single script in R studio. It works.  


```{r echo=TRUE}
rm(list=ls())
# you need to install these packages if have not done so.
#install.packages("devtools")
# devtools::install_github("lchiffon/REmap")
# devtools::install_github("badbye/baidumap"")
install.packages("png")
library("png")

library("REmap")
library("baidumap")

# I realized chinese string have more success match with their coordinates
 city<- c("北京","吉林省", "广东","湖北","重庆","广西","西藏","四川", "云南", "海南")
 begin<-rep("beijing",length(city))   #(起点)
 mapdata<-data.frame(begin,city)
 getBaiduMap2(city, width = 400, height = 400, zoom = 10, scale = 2,
             color = "color", messaging = TRUE)
 map_pic <- remap(mapdata = mapdata,  #数据框
                 title = "Footsteps covered most hotspots of China", #标题
                 subtitle = "Field work activities from 2006-2012",  #副标题
                 theme = get_theme(theme = "Dark")) #主题
plot(map_pic)

```

{{< figure src="Feildwork.gif" title="Field trip trail" >}}
