---
title: "TCGAbiolinks (二)\_获取生存分析数据"
tags: []
id: '1945'
categories:
  - - 数据库
date: 2022-07-03 10:58:37
---

## 来源

[https://www.yisu.com/zixun/572908.html](https://www.yisu.com/zixun/572908.html)

[https://j11.fun/tcgaclinical](https://j11.fun/tcgaclinical)

## 慢更新数据（不推荐）

```R
library(TCGAbiolinks)
PRAD <- GDCquery(project = 'TCGA-PRAD',
                  data.category = "Clinical",
                  file.type = "xml")
GDCdownload(query = PRAD)
clinical <- GDCprepare_clinic(PRAD, clinical.info = 'patient')
saveRDS(clinical, 'prad_clinical.rds')
df <- clinical[,c('bcr_patient_barcode','vital_status', 'days_to_death', 'days_to_last_followup', 'biochemical_recurrence', 'days_to_first_biochemical_recurrence', 'patient_death_reason')]
df[['os_time']] <- with(df, ifelse(vital_status == 'Dead', days_to_death, days_to_last_followup))
```

## 快更新数据

```R
library(TCGAbiolinks)
cl_new <- GDCquery_clinic(project = 'TCGA-PRAD', type = 'clinical')
df <- cl_new[,c('bcr_patient_barcode', 'days_to_last_follow_up', 'days_to_death', 'vital_status', 
         'days_to_last_known_disease_status', 'last_known_disease_status',
         'days_to_recurrence', 'progression_or_recurrence')]
df[['os_time']] <- with(df, ifelse(vital_status == 'Dead', days_to_death, days_to_last_follow_up))
write.csv(df, 'prad_survival.csv')
```

*   **os\_time**为总体生存时间，单位：天
*   **vital\_status**为结局，Dead or Alive

## 补丁

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
patch <- readRDS('prad_clinical.rds')
patch <- patch[f_rm_duplicated(patch$bcr_patient_barcode),]
patch <- patch[patch$patient_death_reason != '',]
patch[,c('bcr_patient_barcode','patient_death_reason')]
df <- df[f_rm_duplicated(df$bcr_patient_barcode),]
df[['os_status']]  <- with(df, ifelse(vital_status == 'Dead', 0, 1))
df[df$bcr_patient_barcode %in% with(patch, 
                                    bcr_patient_barcode[patient_death_reason == 'Other, non-malignant disease']),
   'os_status']  <- 1
df[df$os_status==0,]
write.csv(df, 'prad_survival.csv')
```