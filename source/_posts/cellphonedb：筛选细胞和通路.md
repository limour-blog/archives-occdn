---
title: cellphoneDB：筛选细胞和通路
tags: []
id: '1728'
categories:
  - - 生信
  - - 细胞通讯
date: 2022-04-25 17:47:05
---

```r
f_cpdb_select_pathway <- function(lc_res, grep_i_p_list=NULL){
    if(is.null(grep_i_p_list)){
        grep_i_p_list = list(
            chemokines = "^CXCCCLCCRCX3XCLXCR",
            th1 = "IL2IL12IL18IL27IFNGIL10TNF$TNF LTALTBSTAT1CCR5CXCR3IL12RB1IFNGR1TBX21STAT4",
            th2 = "IL4IL5IL25IL10IL13AREGSTAT6GATA3IL4R",
            th17 = "IL21IL22IL24IL26IL17AIL17AIL17FIL17RAIL10RORCRORASTAT3CCR4CCR6IL23RATGFB",
            treg = "IL35IL10FOXP3IL2RATGFB",
            costimulatory = "CD86CD80CD48LILRB2LILRB4TNFCD2ICAMSLAMLT[AB]NECTIN2CD40CD70CD27CD28CD58TSLPPVRCD44CD55CD[1-9]",
            coinhibitory = "SIRPCD47ICOSTIGITCTLA4PDCD1CD274LAG3HAVCRVSIR",
            niche = "CSF"
        )
    }
    lc_res$grep_i_p_list = list()
    for(name in names(grep_i_p_list)){
        lc_res$grep_i_p_list[[name]] = unique(grep(grep_i_p_list[[name]], lc_res$s_m_p$interacting_pair,value = T))
    }
    lc_res
}
```

```r
f_cpdb_select_cluster <- function(lc_res, grep_celltype){
    lc_res$grep_celltype <- unique(grep(grep_celltype, lc_res$s_m_p$celltype, value = T))
    lc_res
}
```

```r
f_cpdb <- function(lc_res){
    res <- lc_res$s_m_p
    if(!is.null(lc_res$grep_i_p_list)){
        
        res <- res %>% dplyr::filter(interacting_pair %in% reduce(lc_res$grep_i_p_list, c))
    }
    if(!is.null(lc_res$grep_celltype)){
        res <- res %>% dplyr::filter(celltype %in% lc_res$grep_celltype)
    }
    res
}
```

## 示例

```r
sce <- f_readcellphoneDB('crpc_strom_epi_new')
sce <- f_cpdb_select_cluster(sce, 'iCAF')
grep_i_p_list = list(
    vegf = "VEGFFGFIGF" 
)
sce <- f_cpdb_select_pathway(sce, grep_i_p_list)
f_cDB_dotplot(f_cpdb(sce))
```