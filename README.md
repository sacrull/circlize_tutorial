# Circlize Tutorial
This is a very detailed and fantastic tutorial to follow: https://jokergoo.github.io/circlize_book/book/index.html
## Install Packages
```R
install.packages("circlize")
setwd("~/nada_club")
library(circlize)
library(tidyverse)
library(reshape2)
```
## Which cars have 3, 4, or 5 gears?
```R
head(mtcars)
mtcars2 <- rownames_to_column(mtcars, var = "car") #make into long dataframe
colnames(mtcars2)[1] <- "from" #rename the cars column
colnames(mtcars2)[11] <- "to" #rename the gear columns
mtcars2$gears <- mtcars2$to
mtcars3 <- mtcars2 %>% select(from, to)
head(mtcars3)
#               from to
#1         Mazda RX4  4
#2     Mazda RX4 Wag  4
#3        Datsun 710  4
#4    Hornet 4 Drive  3
#5 Hornet Sportabout  3
#6           Valiant  3
pdf("plain_cord.pdf")
chordDiagram(mtcars3)
circos.clear()
dev.off()
```
Clean up the plot
```R
ordered_list <- unique(sort(mtcars3$from)) # get alphaetical order of the cars 
ordered_list2 <- c("5", "4", "3", ordered_list)

mtcars3 <- mtcars2 %>% select(from, to, am, cyl)
mtcars3$to <- as.character(mtcars3$to) #no extra numerics
mtcars3$am <- as.character(mtcars3$am) #no extra numerics
mtcars3$cyl <- as.character(mtcars3$cyl) #no extra numerics
mtcars3 <- cbind(value = 1, mtcars3)
mtcars3 <- mtcars3[, c(2,3,1,4, 5)]
#color the chords
colors <- ifelse(mtcars3$am == "0", "purple", "orange")
#color the ring
border_cols <- as.vector(ifelse(mtcars3$cyl == "4", "blue", ifelse(mtcars3$cyl == "6", "yellow", "pink"))) # colors are provided by Allie
border_cols[33] <- "gray"
border_cols[34] <- "gray"
border_cols[35] <- "gray"
names(border_cols) <- c(mtcars3$from, "3", "4", "5")


pdf("clean_cord.pdf")
chordDiagram(mtcars3, order = ordered_list2, big.gap = 15, transparency = 0.5, col = colors, grid.col = border_cols,
	annotationTrack = c("grid"), preAllocateTracks = list(track.height = 0.1))

circos.trackPlotRegion(track.index = 1, panel.fun = function(x, y) {
	xlim = get.cell.meta.data("xlim")
	xplot = get.cell.meta.data("xplot")
	ylim = get.cell.meta.data("ylim")
	sector.name = get.cell.meta.data("sector.index")
	if(abs(xplot[2] - xplot[1]) < 20) {
	circos.text(mean(xlim), ylim[1], sector.name, facing = "clockwise",
	niceFacing = TRUE, adj = c(0, 0.5), cex=.50)
	} else {
	circos.text(mean(xlim), ylim[1], sector.name, facing = "inside",
	niceFacing = TRUE, adj = c(0.5, 0), cex=.50)
	}
}, bg.border = NA)
dev.off()
```