# Pies-and-maps
a collection of codes for pie-charts on maps

![pie_chart](https://github.com/Vjimenez-vasquez/Pies-and-maps/assets/89874227/5fa7d6a1-6274-4728-a7ea-45a0d3c193fe)

# step 1 : load libraries
```r
setwd("C:/Users/THINKSTATION/Desktop/DJ.1/mapa")
getwd()
# 1 # install.packages("scatterpie") #
library(rgdal)
library(scatterpie)
library(ggplot2)
```

# step 2 : load and plot shapefiles
```r
# 1 # read shapefile #
dp <- readOGR("departamentos/DEPARTAMENTOS_inei_geogpsperu_suyopomalia.shp")
plot(dp, border="red")
per <- fortify(dp)
centroids.df <- as.data.frame(coordinates(per))

# 2 # plot shapefile #
mapplot3 <- ggplot(data = per, aes(long, lat, group=group)) +
  		geom_polygon(color = "black", fill  = "white")
mapplot3
```

# step 3 : load metadata and coordinates
```r
# 1 # read shapefile #
d <- read.csv("lat_longs.tsv", header=T, sep="\t")
g <- read.csv("metadata.tsv", header=T, sep="\t")
gp <- g[g$country %in% "Peru" & !g$lineage_PANGOLIN %in% "BA.5.1",]
gp$n <- rep(1,length(gp$EPI))

lineage <- sort(unique(gp$lineage_PANGOLIN))
departm <- sort(unique(gp$region))
```

# step 4 : estimate frequencies 
```r
# 1 # prepare the loop #
a0 <- 0 ;a1 <- 0 ;a2 <- 0 ;a3 <- 0 ;a4 <- 0 ;a5 <- 0
for (i in departm){
	for (j in lineage){
a0 <- sum(gp[gp$lineage_PANGOLIN %in% j & gp$region %in% i, 13])
a1 <- sum(gp[gp$region%in% i, 13])
a2 <- (a0/a1)*100

a3 <- append(a3,a2)
a4 <- append(a4,i)
a5 <- append(a5,j)

}
}

a6 <- data.frame(a5,a4,a3)
a7 <- a6[order(a6$a4,a6$a5),]
```

# step 5 : estimate frequencies 
```r
# 1 # prepare the loop #
library(fossil)
df <- create.matrix(a7[2:length(a7$a5),], tax.name = "a4",
   locality = "a5",
   abund.col = "a3",
   abund = TRUE)
df <- as.data.frame(df)
df$region <- row.names(df)

t <- aggregate(gp$n, by=list(gp$region),FUN=sum)
names(t) <- c("region","count")

abundance <- merge(df,t,by="region",all.x=T)
data <- merge(d,abundance,by="region",all.x=T)
data$radius <- log(data$count)

# 2 # add pies #
mapplot3 + geom_scatterpie(aes(x=lat, y=long), data=data, 
	     cols=c("BA.5.1.25","DJ.1","DJ.1.1","DJ.1.2","DJ.1.3")) + coord_fixed()

mapplot4 <- mapplot3 + geom_scatterpie(aes(x=lat, y=long, r=radius/8), data=data, 
	     cols=c("BA.5.1.25","DJ.1","DJ.1.1","DJ.1.2","DJ.1.3")) + coord_fixed()

mapplot4 + theme_bw() + scale_fill_discrete(name="Lineage")
```
