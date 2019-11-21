---
title: Python 常用命令
date: 2018-05-28 14:25:00
tags: Python
---

## 更新所有包
pip freeze --local | grep -v '^\-e' | cut -d = -f 1  | xargs pip install -U

## 虚拟环境

### 新建
virtualenv --no-site-packages venv

### 激活
* source venv/bin/activate (Lunix)
* venv\Scripts\activate (Windows)

### 和退出
deactivate

## Miniconda

### 安装
* 下载 [Miniconda3-latest-Linux-x86_64.sh](https://conda.io/miniconda.html)
* `bash Miniconda3-latest-Linux-x86_64.sh`

### 更新
```
conda update conda
```

### 创建环境
```
conda create --name scrapy scrapy
```

### 激活环境
```
source activate scrapy
```

### 停用环境
```
source deactivate
```

### 列出环境
```
conda info --envs
```
