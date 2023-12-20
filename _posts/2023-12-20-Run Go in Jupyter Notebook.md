---
layout:     post
title:      Run Go in Jupyter Notebook
subtitle:   Jupyer Notebook is a effective tool for newbies to learn and practice a brand new programming language.
date:       2023-12-20
author:     Pele
header-img: img/jupyter_dark.webp
catalog: true
tags:
    - Go
---

> Jupyer Notebook is a effective tool for newbies to learn and practice a brand new programming language. It is easy to install and use. This article will show you how to install and run Go in Jupyter Notebook on macOS.

## 1. Install Go and Jupyter Notebook

```bash
# Install Go
brew install go

# Install Jupyter Notebook
pip3 install jupyter
```

## 2. Install Go kernel for Jupyter Notebook

[gophernotes - Use Go in Jupyter notebooks and nteract
](https://github.com/gopherdata/gophernotes?tab=readme-ov-file#mac)

```bash
  go install github.com/gopherdata/gophernotes@v0.7.5
  mkdir -p ~/Library/Jupyter/kernels/gophernotes
  cd ~/Library/Jupyter/kernels/gophernotes
  cp "$(go env GOPATH)"/pkg/mod/github.com/gopherdata/gophernotes@v0.7.5/kernel/*  "."
  chmod +w ./kernel.json # in case copied kernel.json has no write permission
  sed "s|gophernotes|$(go env GOPATH)/bin/gophernotes|" < kernel.json.in > kernel.json
```

To confirm that the gophernotes binary is installed in GOPATH, execute it directly:

```bash
 (go env GOPATH)"/bin/gophernotes
```

and you should see the following:

`2017/09/20 10:33:12 Need a command line argument specifying the connection file.`

## 3. Run Jupyter Notebook
- Start the jupyter notebook serve
```bash
jupyter notebook
```
- mSelect `Go` from the kernel menu.

- Have fun!

