---
title: inferCNV推断肿瘤染色体变异：绘图
tags: []
id: '1597'
categories:
  - - 染色体变异
  - - 生信
date: 2022-03-01 21:00:38
---

## 第一步 获取染色体长短臂位置注释

*   http://hgdownload.cse.ucsc.edu/goldenpath/hg38/database/
*   wget http://hgdownload.cse.ucsc.edu/goldenpath/hg38/database/cytoBand.txt.gz
*   gunzip cytoBand.txt.gz

```r
f_Rbisect_n <- function(lst, value){
    low=1
    high=length(lst)
    mid=length(lst)%/%2
    if(high == low){return(mid)}
    if (lst[low]>=value) {return(low)}
    else if (lst[high]<=value){return(high)}
    else{
        while (lst[mid] != value) {
            if (value > lst[mid]){
                low = mid + 1
            }else{
                high = mid - 1
            }
            if(high<low){
                break
            }
            mid=(low+high)%/%2
        }
    }
    if(lst[mid] == value){
        return(mid)
    }
    if(mid == 1){
        tmp <- (lst[1] + lst[2])/2
        if(value < tmp){
            return(1)
        }else{
            return(2)
        }
    }else if (mid == length(lst)){
        tmp <- (lst[mid-1] + lst[mid])/2
        if(value < tmp){
            return(mid-1)
        }else{
            return(mid)
        }
    }else{
        if(lst[mid] > value){
            tmp <- (lst[mid-1] + lst[mid])/2
            if(value < tmp){
                return(mid-1)
            }else{
                return(mid)
            }
        }else{
            tmp <- (lst[mid+1] + lst[mid])/2
            if(value < tmp){
                return(mid)
            }else{
                return(mid+1)
            }
        }
    }
}
```

```r
cB <- read.table('cytoBand.txt', sep = '\t', header = F)
cB <- cB[cB$V1 %in% paste0('chr', c(1:22,'X', 'Y')),]
f_get_cytoBand_name <- function(chN, chs, che, cB){
    cB <- cB[cB$V1 == chN,]
    chs <- f_Rbisect_n(cB$V2, chs)
    che <- f_Rbisect_n(cB$V3, che)
    cB[chs:che,]
}
```

## 第二步 对染色体区间进行注释

*   HMM\_CNV\_predictions.HMMi6.rand\_trees.hmm\_mode-subclusters.Pnorm\_0.5.pred\_cnv\_regions.dat
*   第三列state的定义如下：
*   状态 1：0x：完全丢失
*   状态 2：0.5x：丢失一份
*   状态 3：1x：中性
*   状态4：1.5x：增加一份
*   状态 5：2x：增加两份
*   状态 6：3x：增加大于两份

```r
f_infCNV_HMM_regions <- function(HMM_p, cB, s_f=T){
    HMM_p <- HMM_p[HMM_p$state != 3, ]
    res <- HMM_p[c('cell_group_name','state', 'cnv_name')]
    res['cnv_name_s'] <- res['cnv_name']
    for(i in 1:nrow(res)){
        tmp <- with(HMM_p[i,], f_get_cytoBand_name(chr, start, end, cB))
        tmp2 <- tmp[c(1,nrow(tmp)),]
        tmp <- paste0(tmp$V1,tmp$V4)
        tmp <- Reduce(x = tmp, f = function(x,y){paste0(x, '; ', y)})
        res[i, 'cnv_name'] <- tmp
        tmp2 <- paste0(tmp2[1, 'V1'], tmp2[1, 'V4'], '-', tmp2[2, 'V4'])
        res[i, 'cnv_name_s'] <- tmp2
    }
    res['state_d'] <- c('loss', 'loss', '1x', 'gain', 'gain', 'gain')[res$state]
    res['cnv_name_ss'] <- paste(res$cnv_name_s, res$state_d, sep = '_')
    if(s_f){
        return(res)
    }else{
        return(res[c('cell_group_name', 'cnv_name_ss')])
    }
}
```

```r
HMM_p3 <- read.table('p3/HMM_CNV_predictions.HMMi6.rand_trees.hmm_mode-subclusters.Pnorm_0.5.pred_cnv_regions.dat', sep = '\t', header = T)
write.csv(f_infCNV_HMM_regions(HMM_p3, cB), file='HMM_p3.csv', row.names=F)
```

## 第三步 绘制基础图

```r
output_dir = 'p1_p'
if (!file.exists(output_dir)){dir.create(output_dir)}
infercnv::plot_cnv(infercnv_obj_p1, #上两步得到的infercnv对象
                   plot_chr_scale = F, #画染色体全长，默认只画出（分析用到的）基因
                   output_filename = "p1_p/better_plot_p1", output_format = "pdf", cluster_by_groups=F,#保存为pdf文件
                   custom_color_pal =  color.palette(c("#8DD3C7","white","#BC80BD"), c(2, 2))) #改颜色
```

## 第四步 绘制进化树

*   17\_HMM\_predHMMi6.rand\_trees.hmm\_mode-subclusters.cell\_groupings

```shell
cp -r ../../python/uphyloplot2-master p1
cd p1
rm uphyloplot2-master/Inputs/*
head 17_HMM_predHMMi6.rand_trees.hmm_mode-subclusters.cell_groupings -n 1 > uphyloplot2-master/Inputs/test.cell_groupings
less -S 17_HMM_predHMMi6.rand_trees.hmm_mode-subclusters.cell_groupings  grep "observations"  less -S >> uphyloplot2-master/Inputs/test.cell_groupings
conda activate rinferCNV
cd uphyloplot2-master/
python uphyloplot2.py
```