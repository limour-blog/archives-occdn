---
title: cBioPortal的原始数据获取
tags: []
id: '1956'
categories:
  - - 数据库
date: 2022-07-07 00:08:33
---

## 来源

[https://github.com/cBioPortal/datahub/tree/master/public](https://github.com/cBioPortal/datahub/tree/master/public)

[https://www.cbioportal.org/study/summary?id=prad\_su2c\_2019](https://www.cbioportal.org/study/summary?id=prad_su2c_2019)

## 第一步 获取数据

*   ~/dev/xray/xray -c ~/etc/xui2.json &
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://github.com/cBioPortal/datahub/raw/master/public/prad\_su2c\_2019/data\_mrna\_seq\_fpkm\_capture.txt -O data\_mrna\_seq\_fpkm\_capture.txt
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://github.com/cBioPortal/datahub/raw/master/public/prad\_su2c\_2019/data\_clinical\_sample.txt -O data\_clinical\_sample.txt
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://github.com/cBioPortal/datahub/raw/master/public/prad\_su2c\_2019/data\_clinical\_patient.txt -O data\_clinical\_patient.txt

```R
d <- read.table('data_mrna_seq_fpkm_capture.txt', header = T, sep = '\t', allowEscapes = T, quote = '')
d
meta <- read.table('data_clinical_sample.txt', header = T, sep = '\t', comment.char = '#')
meta
clinical <- read.table('data_clinical_patient.txt', header = T, sep = '\t', comment.char = '#')
clinical
```

## 第二步 获取生存分析的数据

```R
f_rm_duplicated <- function(NameL, reverse=F){
    tmp <- data.frame(table(NameL))
    if(reverse){
        tmp <- tmp$NameL[tmp$Freq > 1]
    }else{
        tmp <- tmp$NameL[tmp$Freq == 1]
    }
    which(NameL %in% as.character(tmp))
}
f_name_dedup <- function(lc_exp, rowN = 1){
    if (rowN == 0){
        res <- lc_exp
        rowNn <- rownames(lc_exp)
    }else{
        res <- lc_exp[-rowN]
        rowNn <- lc_exp[[rowN]]
    }
    noDup <- f_rm_duplicated(rowNn)
    tmp <- rowNn[noDup]
    noDup <- res[noDup,]
    rownames(noDup) <- tmp
    Dup <- f_rm_duplicated(rowNn, T)
    rowNn <- rowNn[Dup]
    Dup <- res[Dup,]
    rownames(Dup) <- NULL
    lc_tmp = by(Dup,
         rowNn,
         function(x) rownames(x)[which.max(rowMeans(x))])
    lc_probes = as.integer(lc_tmp)
    Dup = Dup[lc_probes,]
    rownames(Dup) <- rowNn[lc_probes]
    return(rbind(noDup,Dup))
}
meta <- meta[f_rm_duplicated(meta$PATIENT_ID),]
rownames(meta)<- meta$PATIENT_ID
meta
rownames(clinical) <- clinical$PATIENT_ID
clinical
mergeID <- intersect(rownames(clinical), rownames(meta))
df <- cbind(clinical[mergeID,], meta[mergeID,])
rownames(df) <- df$SAMPLE_ID
df
saveRDS(df, 'meta.rds')
saveRDS(d, 'fpkm.rds')
```