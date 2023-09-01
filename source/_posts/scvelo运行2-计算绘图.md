---
title: 'scVelo运行2: 计算绘图'
tags: []
id: '1817'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-08 19:17:32
---

## 计算

```python
scv.pp.moments(adata, n_pcs=30, n_neighbors=30)

scv.tl.recover_dynamics(adata, n_jobs=8)

scv.tl.velocity(adata, mode='dynamical')

scv.tl.velocity_graph(adata, n_jobs=8)

adata.write('hPB003.h5ad')
```

## 绘图

```python
from matplotlib.pyplot import rc_context
with rc_context({'figure.figsize': (12, 12)}):
    scv.pl.velocity_embedding_stream(adata, basis='umap', color=['cell_type_fig3'], save = "hPB003 velocity embedding stream.svg")
```