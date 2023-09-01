---
title: 'scVelo运行1: 合并数据'
tags: []
id: '1806'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-08 11:53:02
---

[https://www.bilibili.com/read/cv7751387](https://www.bilibili.com/read/cv7751387?spm_id_from=333.999.0.0)

[https://www.bilibili.com/read/cv7760100](https://www.bilibili.com/read/cv7760100?spm_id_from=333.999.0.0)

[https://www.bilibili.com/read/cv7764833](https://www.bilibili.com/read/cv7764833)

[https://www.jianshu.com/p/1e7acde1a318](https://www.jianshu.com/p/1e7acde1a318)

## 第一步 导入模块

```python
import scvelo as scv
import scanpy as sc
import numpy as np
import pandas as pd
import seaborn as sns 
scv.settings.verbosity = 3  # show errors(0), warnings(1), info(2), hints(3)
scv.settings.set_figure_params('scvelo')  # for beautified visualization
```

## 第二步 读取数据（loom文件读取时间很长）

```python
loomf = '/home/jovyan/upload/zl_liu/data/data/res/hPB003/velocyto/hPB003.loom'
adata = scv.read(loomf, cache=False)
metadataf = '/home/jovyan/upload/zl_liu/data/data/res/hPB003/velocyto/metadata.csv'
meta = pd.read_csv(metadataf, index_col=0)
```

## 第三步 取交集并合并数据

```python
tmp = [x for x in  (x[7:23] for x in adata.obs.index) if x in meta.index]
meta = meta.loc[tmp]
adata = adata[[f'hPB003:{x}x' for x in tmp]]

test = meta['cell_type_fig3']
test.index = adata.obs.index
adata.obs['cell_type_fig3'] = test

adata.obsm['X_pca'] =  np.asarray(meta.iloc[:, 4:])
adata.obsm['X_umap'] = np.asarray(meta.iloc[:, 2:4])

sc.pl.pca(adata, color='cell_type_fig3')
sc.pl.umap(adata, color='cell_type_fig3')
```