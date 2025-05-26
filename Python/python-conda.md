---
pubDatetime: 2023-09-27 21:03:53
title: Python Anaconda
slug: Python Anaconda
featured: false
draft: false
tags:
  - Python
  - Anaconda
description: "Python 版本控制工具 Anaconda"
---

Anaconda 是一个 python 版本管理器, 能快速创建虚拟环境, 管理 python 版本,安装包等.

## Table of Contents

## 安装

[Anaconda 官网](https://www.anaconda.com/) 下载对应平台安装包
[国内镜像](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

## 常用命令

```bash
 $ conda info                                              # 查看 anaconda 基本信息
 $ conda --version                                         # 查看 conda 版本
 > conda 22.9.0

 $ conda env list                                          # 列出当前所有环境
 $ conda info -e                                           # 列出当前所有环境
 > # conda environments:
 > #
 > base                  *  /root/anaconda3                # 带 * 表示当前使用环境
 > py3.9                    /root/anaconda3/envs/py3.9     # 已创建环境

 $ conda update -n base -c defaults conda                  # 更新 conda 版本
 $ conda update -n base conda
```

## 创建环境

```bash
 $ conda create -n <name> python=<version>                 # 选择 python 版本创建虚拟环境并命名

 $ conda create -n py3.7 python=3.7                        # 创建 python 3.7 虚拟环境
 > Collecting package metadata (current_repodata.json): done
 > Solving environment: done
 > ......                                                  # 下载python版本包
 > Retrieving notices: ...working... done
```

## 启用退出

```bash
 $ conda activate <name>                                   # 激活指定环境
 $ conda activate py3.7                                    # 激活虚拟环境

 $ conda deactivate                                        # 退出当前环境

 $ conda rename -n <old name> <new name>                   # 重命名环境
 $ conda rename -n py3.9 py39
 > Source:      /root/anaconda3/envs/py3.9
 > Destination: /root/anaconda3/envs/py39
 > Packages: 21
 > Files: 926
 > Preparing transaction: done
 > Verifying transaction: done
 > Executing transaction: done
```

## 删除环境

```bash
 $ conda env remove -n <name>                               # 删除指定环境

 $ conda env remove -n py3.7                                # 删除 py3.7 环境
 > Remove all packages in environment /root/anaconda3/envs/py3.7:
```

## 包管理

```bash
 $ conda list -n <name>
 $ conda list -n py3.7                                      # 列出环境内所有包
 > # packages in environment at /root/anaconda3/envs/py39:
 > #
 > # Name
 > ......

 $ conda install -n <name> <pkg1> <pkg2>                   # 指定环境安装包
 $ conda install -n  py39  toml  yaml                      # py39 环境安装 toml 和 yaml 包

 $ conda uninstall <pkg> -n <name>                         # 指定环境卸载包
 $ conda uninstall yaml -n py39

 $ conda update --all -n <name>                            # 指定环境更新所有包
```

## 更换源

查看当前 conda 源

```bash
 $ conda config --show channels
 > channels:
 > - defaults
```

用户路径下 `.condarc` 文件修改源, 无文件则创建

- linux: $USER/.condarc
- windows: C:\Users\%USER%\.condarc

```bash
 $ cat ~/.condarc
 > channels:
 > - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
 > - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
 > - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

 $ conda config --show channels
 > channels:
 >  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/cond-forge/
 >  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
 >  - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

国内源

[南方科大源](https://mirrors.sustech.edu.cn/help/anaconda.html#introduction)
[中科大源](https://mirrors.ustc.edu.cn/help/anaconda.html)
[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

```bash
channels:
  - defaults
show_channel_urls: true
default_channels:
  # 中科大
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/main
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/r
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/pro
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/msys2
  
  # 南方科技
  - https://mirrors.sustech.edu.cn/anaconda/pkgs/main
  - https://mirrors.sustech.edu.cn/anaconda/pkgs/free
  - https://mirrors.sustech.edu.cn/anaconda/pkgs/r
  - https://mirrors.sustech.edu.cn/anaconda/pkgs/msys2
  
  # 清华
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2

custom_channels:
  conda-forge: https://mirrors.sustech.edu.cn/anaconda/cloud
  msys2: https://mirrors.sustech.edu.cn/anaconda/cloud
  bioconda: https://mirrors.sustech.edu.cn/anaconda/cloud
  menpo: https://mirrors.sustech.edu.cn/anaconda/cloud
  pytorch: https://mirrors.sustech.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.sustech.edu.cn/anaconda/cloud
  nvidia: https://mirrors.sustech.edu.cn/anaconda-extra/cloud
```

```bash
# 自定义版本存放位置
envs_dirs:
  - D:\Mimiforge\envs

# 自定义包存放位置
pkgs_dirs:
  - D:\Mimiforge\pkgs
```

## miniforge

Anaconda 占用空间较大, 可以使用 miniforge 作为替代, 仅包含 conda, python, pip 三个包  
[miniforge 清华源](https://mirrors.tuna.tsinghua.edu.cn/github-release/conda-forge/)

```bash
 $ bash Miniforge3-4.10.3-Linux-x86_64.sh
 
 # mamba 是 miniforge 的包管理器, 速度更快, 兼容 conda 环境和命令
 $ mamba env list
 > # conda environments:
 > #
 > base                  *  /root/mambaforge
 
 # 创建环境
 $ mamba create -n py3.12 python=3.12

 # 启用退出
 $ mamba activate py3.12
 $ mamba deactivate

 # 包管理
 $ mamba install <pkg> -n <name>
 $ mamba install toml -n py3.12
 $ mamba uninstall <pkg> -n <name>

 # 查看 mamba 源(配置 ~/.condarc)
 $ mamba info
```

注: Windows 需设置环境变量 `Miniforge\Scripts`, 使用下载源为用户路径下 `.condarc` 文件修改源

## UV

[uv](https://github.com/astral-sh/uv)是一个使用 RUST 实现的 python 包管理器, 提供全面的项目管理

```bash
 # 安装
 $ curl -LsSf https://astral.sh/uv/install.sh | sh

 # 指定 python 版本创建项目(自动创建 git 仓库)
 $ uv init test -p  3.13 && cd test
test
├── .gitignore
├── main.py
├── pyproject.toml   # 包管理配置文件, 依赖列表
├── .python-version  # 指定 python 运行版本
└── README.md

 # 同步项目依赖(按版本创建虚拟环境 venv，安装项目依赖)
 $ uv sync

 # 添加/删除依赖(自动添加到 pyproject.toml)
 $ uv add pandas
 $ uv remove pandas

 # 使用 requirements.txt 添加依赖
 $ uv add -r requirements.txt

 # 显示依赖列表
 $ uv tree
 test v0.1.0
├── loguru v0.7.3
└── toml v0.10.2
 
 # 显示可下载的 python 版本
 $ uv python list
cpython-3.14.0a6-linux-x86_64-gnu                 /root/.local/share/uv/python/cpython-3.14.0a6-linux-x86_64-gnu/bin/python3.14
cpython-3.14.0a6+freethreaded-linux-x86_64-gnu    <download available>
cpython-3.13.3-linux-x86_64-gnu                   /root/.local/share/uv/python/cpython-3.13.3-linux-x86_64-gnu/bin/python3.13
cpython-3.13.3+freethreaded-linux-x86_64-gnu      <download available>
```
