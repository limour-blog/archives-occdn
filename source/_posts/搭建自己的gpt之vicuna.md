---
title: 搭建自己的GPT之vicuna
tags: []
id: '2710'
categories:
  - - AIGC
  - - 开源
date: 2023-04-23 20:09:58
---

Vicuna是基于Meta的LLaMa开发的chatbot。模型参数[点此](https://huggingface.co/eachadea/ggml-vicuna-13b-1.1)，模型框架[点此](https://github.com/abetlen/llama-cpp-python)。

## 下载模型

*   mkdir -p ~/model && cd ~/model
*   wget https://huggingface.co/eachadea/ggml-vicuna-13b-1.1/resolve/main/ggml-vicuna-13b-1.1-q4\_3.bin

## 基于conda

*   conda create -n llama -c conda-forge python=3.8
*   conda activate llama
*   pip install llama-cpp-python\[server\] -i https://pypi.tuna.tsinghua.edu.cn/simple
*   export MODEL=./ggml-vicuna-13b-1.1-q4\_3.bin
*   export PORT=1234
*   export HOST=0.0.0.0
*   python -m llama\_cpp.server

## 构建Docker镜像

```Dockerfile
FROM continuumio/miniconda3:latest
RUN /bin/bash -c "\
    conda create -n llama -c conda-forge python=3.8 -y\
    && conda install -n llama compilers make -c conda-forge -y\
    && conda run -n llama pip install llama-cpp-python[server] -i https://pypi.tuna.tsinghua.edu.cn/simple"
ENV MODEL=/llama/model.bin
ENV HOST=0.0.0.0
ENV PORT=1234
CMD ["/opt/conda/envs/llama/bin/python3.8", "-m", "llama_cpp.server"]
```

*   mkdir -p ~/app/llama && cd ~/app/llama && nano Dockerfile && nano docker-compose.yml
*   docker build -t limour/llama .
*   docker run --rm -it limour/llama /bin/bash

## 部署Docker镜像

```yml
version: '3.3'
services:
    llama:
        ports:
            - '1234:1234'
        restart: always
        volumes:
            - '/home/gene/upload/zl_liu/vicuna/ggml-vicuna-13b-1.1-q4_3.bin:/llama/model.bin'
        image: limour/llama
        command: ["/opt/conda/envs/llama/bin/python3.8", "-m", "llama_cpp.server"]
```

*   nano docker-compose.yml
*   sudo docker-compose up -d
*   sudo docker-compose logs

## 查看文档

*   访问 http://localhost:1234/docs