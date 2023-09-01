---
title: inferCNV推断肿瘤染色体变异
tags: []
id: '1581'
categories:
  - - 染色体变异
  - - 生信
date: 2022-02-28 23:59:29
---

## 第一步 获取对应物种的基因位置注释文件

*   https://www.gencodegenes.org/human/
*   wget https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode\_human/release\_39/gencode.v39.chr\_patch\_hapl\_scaff.annotation.gtf.gz
*   gunzip gencode.v39.chr\_patch\_hapl\_scaff.annotation.gtf.gz
*   hg39 <- read.table('gencode.v39.chr\_patch\_hapl\_scaff.annotation.gtf', sep = '\\t', comment.char = "#")
*   hg39\['gene\_id'\] <- stringr::str\_extract(hg39$V9,"(?<=gene\_id ).+?(?=;)")
*   hg39\['gene\_name'\] <- stringr::str\_extract(hg39$V9,"(?<=gene\_name ).+?(?=;)")
*   或者直接使用TCGA的基因位置注释：[https://file-cdn.limour.top/bix/22.02.18.tcga\_rowRanges.csv.gz](https://file-cdn.limour.top/bix/22.02.18.tcga_rowRanges.csv.gz)

## 第二步 获取分析数据的基因位置信息

```r
f_df_order <- function(df, oN, groupN, decreasing = T){
    df <- as.data.frame(df)
    for(oi in levels(df[[groupN]])){
        idx <- df[[groupN]] == oi
        tmp <- df[idx, ]
        tmp <- tmp[order(tmp[[oN]], decreasing = decreasing),]
        df[idx, ] <- tmp
    }
    df
}
```

```r
tcga_gene <- read.csv('22.02.18.tcga_rowRanges.csv', row.names = 1)
tcga_gene['seqnames'] <- factor(tcga_gene$seqnames, levels = paste0('chr', c(1:22,'X', 'Y')))
tcga_gene <- tcga_gene[order(as.numeric(tcga_gene$seqnames)),]
tcga_gene <- f_df_order(tcga_gene, 'start', 'seqnames', F)
```

```r
sce <- readRDS('~/upload/zl_liu/data/pca.rds')
sce@meta.data <- readRDS('../fig1/E_meta.data.rds')
tcga_gene <- tcga_gene[tcga_gene$external_gene_name %in% rownames(sce), c('external_gene_name', 'seqnames', 'start', 'end')]
write.table(x = tcga_gene, file = 'gencode_downsampled.txt', sep = '\t', row.names = F, col.names = F, quote = F)
```

## 第三步 导出 raw\_counts\_matrix 和 annotations\_file

```r
library(Seurat)
sce <- readRDS('~/upload/zl_liu/data/pca.rds')
sce@meta.data <- readRDS('../fig1/E_meta.data.rds')
counts_matrix = GetAssayData(seurat_obj, slot="counts")
saveRDS(round(counts_matrix, digits=3), "sc.10x.counts.matrix")
```

```r
df <- sce@meta.data
df <- subset(df, cell_type_fig1A %in% c('Fibroblasts', 'Luminal', 'Endothelial'))

tmp <- subset(df, patient_id == 'patient1')['cell_type_fig1A']
write.table(x = tmp, file = 'annotations_file_p1.txt', sep = '\t', row.names = T, col.names = F, quote = F)

tmp <- subset(df, patient_id == 'patient3')['cell_type_fig1A']
write.table(x = tmp, file = 'annotations_file_p3.txt', sep = '\t', row.names = T, col.names = F, quote = F)

tmp <- subset(df, patient_id == 'patient4')['cell_type_fig1A']
write.table(x = tmp, file = 'annotations_file_p4.txt', sep = '\t', row.names = T, col.names = F, quote = F)

tmp <- subset(df, patient_id == 'patient5')['cell_type_fig1A']
write.table(x = tmp, file = 'annotations_file_p5.txt', sep = '\t', row.names = T, col.names = F, quote = F)
```

## 第四步 进行分析

```r
fuck <- read.table('gencode_downsampled.txt', sep = '\t')
fuck <- fuck[!duplicated(fuck$V1),]
write.table(x = fuck, file = 'gencode_downsampled.txt', sep = '\t', row.names = F, col.names = F, quote = F)
```

```r
infercnv_obj_p1 = CreateInfercnvObject(raw_counts_matrix=readRDS("sc.10x.counts.matrix"),
                                    annotations_file="annotations_file_p1.txt",
                                    delim="\t",
                                    gene_order_file="gencode_downsampled.txt",
                                    ref_group_names=c('Fibroblasts', 'Endothelial') 
                                    )
infercnv_obj_p3 = CreateInfercnvObject(raw_counts_matrix=readRDS("sc.10x.counts.matrix"),
                                    annotations_file="annotations_file_p3.txt",
                                    delim="\t",
                                    gene_order_file="gencode_downsampled.txt",
                                    ref_group_names=c('Fibroblasts', 'Endothelial') 
                                    )
infercnv_obj_p4 = CreateInfercnvObject(raw_counts_matrix=readRDS("sc.10x.counts.matrix"),
                                    annotations_file="annotations_file_p4.txt",
                                    delim="\t",
                                    gene_order_file="gencode_downsampled.txt",
                                    ref_group_names=c('Fibroblasts', 'Endothelial') 
                                    )
infercnv_obj_p5 = CreateInfercnvObject(raw_counts_matrix=readRDS("sc.10x.counts.matrix"),
                                    annotations_file="annotations_file_p5.txt",
                                    delim="\t",
                                    gene_order_file="gencode_downsampled.txt",
                                    ref_group_names=c('Fibroblasts', 'Endothelial') 
                                    )
```

```r
infercnv_obj_p1 = infercnv::run(infercnv_obj_p1,
                             cutoff=0.1, # use 1 for smart-seq, 0.1 for 10x-genomics
                             out_dir="p1", # dir is auto-created for storing outputs
                             cluster_by_groups=F, 
                             analysis_mode="subclusters",
                             tumor_subcluster_partition_method = 'random_trees',
                             denoise=TRUE,
                             HMM=TRUE,
                             num_threads=12)
saveRDS(infercnv_obj_p1, 'infercnv_obj_p1.rds')
system(paste("/opt/conda/bin/python3 /home/jovyan/upload/zl_liu/wecomchan.py", "'task p1 successfully completed'"), intern = T)

infercnv_obj_p3 = infercnv::run(infercnv_obj_p3,
                             cutoff=0.1, # use 1 for smart-seq, 0.1 for 10x-genomics
                             out_dir="p3", # dir is auto-created for storing outputs
                             cluster_by_groups=F, 
                             analysis_mode="subclusters",
                             tumor_subcluster_partition_method = 'random_trees',
                             denoise=TRUE,
                             HMM=TRUE,
                             num_threads=12)
saveRDS(infercnv_obj_p3, 'infercnv_obj_p3.rds')
system(paste("/opt/conda/bin/python3 /home/jovyan/upload/zl_liu/wecomchan.py", "'task p3 successfully completed'"), intern = T)

infercnv_obj_p4 = infercnv::run(infercnv_obj_p4,
                             cutoff=0.1, # use 1 for smart-seq, 0.1 for 10x-genomics
                             out_dir="p4", # dir is auto-created for storing outputs
                             cluster_by_groups=F, 
                             analysis_mode="subclusters",
                             tumor_subcluster_partition_method = 'random_trees',
                             denoise=TRUE,
                             HMM=TRUE,
                             num_threads=12)
saveRDS(infercnv_obj_p4, 'infercnv_obj_p4.rds')
system(paste("/opt/conda/bin/python3 /home/jovyan/upload/zl_liu/wecomchan.py", "'task p4 successfully completed'"), intern = T)

infercnv_obj_p5 = infercnv::run(infercnv_obj_p5,
                             cutoff=0.1, # use 1 for smart-seq, 0.1 for 10x-genomics
                             out_dir="p5", # dir is auto-created for storing outputs
                             cluster_by_groups=F, 
                             analysis_mode="subclusters",
                             tumor_subcluster_partition_method = 'random_trees',
                             denoise=TRUE,
                             HMM=TRUE,
                             num_threads=12)
saveRDS(infercnv_obj_p5, 'infercnv_obj_p5.rds')
system(paste("/opt/conda/bin/python3 /home/jovyan/upload/zl_liu/wecomchan.py", "'task p5 successfully completed'"), intern = T)
```