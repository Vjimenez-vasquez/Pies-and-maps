# Pies-and-maps
a collection of codes for pie-charts on maps

# step 1 : load libraries
```r
setwd("C:/Users/THINKSTATION/Desktop/DJ.1/mapa")
getwd()
# 1 # install.packages("scatterpie") #
library(rgdal)
library(scatterpie)
library(ggplot2)
```
# step 2 : load libraries
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
