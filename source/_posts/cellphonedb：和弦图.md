---
title: cellphoneDB：和弦图
tags: []
id: '1730'
categories:
  - - 生信
  - - 细胞通讯
date: 2022-04-25 19:06:19
---

```r
require(tidyverse)
require(circlize)
f_cpdb_circlize <- function(lc_dir, lc_SOURCE = NULL, lc_TARGET = NULL, return_net = F, track.height=0.3){
    mynet <- read.delim(file.path(lc_dir, "out","count_network.txt"), check.names = FALSE)
    if(!is.null(lc_SOURCE)){
        mynet <- dplyr::filter(mynet, SOURCE %in% lc_SOURCE)
    }
    if(!is.null(lc_TARGET)){
        mynet <- dplyr::filter(mynet, TARGET %in% lc_TARGET)
    }
    chordDiagram(mynet, annotationTrack = c("grid", "axis"), preAllocateTracks = 1)
    circos.trackPlotRegion(track.height=track.height, ylim = c(0, 1), panel.fun = function(x, y) {
        xlim = get.cell.meta.data("xlim")
        ylim = get.cell.meta.data("ylim")
        sector.name = get.cell.meta.data("sector.index")
        xplot = get.cell.meta.data("xplot")
        circos.text(mean(xlim), 0.5, sector.name, facing = "clockwise", niceFacing = T, track.index = 3)
    }, bg.border = NA)
    circos.clear()
    if(return_net){
        return(mynet)
    }
}
```

```r
f_cpdb_circlize('crpc_strom_epi_new')
```