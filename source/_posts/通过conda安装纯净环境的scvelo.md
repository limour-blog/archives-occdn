---
title: 通过conda安装纯净环境的scVelo
tags: []
id: '1777'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-01 13:02:40
---

[https://scvelo.readthedocs.io/about/](https://scvelo.readthedocs.io/about/)

*   conda install -c conda-forge widgetsnbextension -y
*   jupyter nbextension enable --py widgetsnbextension
*   重启 jupyter
*   conda create -n scVelo -c conda-forge python=3.7 -y
*   conda activate scVelo
*   conda install -c conda-forge scanpy -y
*   conda install -c conda-forge matplotlib -y
*   pip install -U scvelo
*   pip install -U tqdm ipywidgets
*   conda install -c conda-forge ipykernel -y
*   python -m ipykernel install --user --name python-scVelo