---
title: docker的相关知识
date: 2023-10-23 11:30:42
tags:
---

# docker镜像和容器的关系

镜像（image）是一个模板，由这个模板构建一个容器（container），容器看作是一个实例，按照面向对象来理解可以说：image是类，container是由类创建的实例。

# Dockerfile

Dockerfile是构建docker镜像的说明书，在这个文件中写入构建过程可以实现自动化构建。

## 编写

```Dockerfile
from UBuntu 
#构建镜像的基础镜像，理解为以这个镜像为基础进行下面的构建
```

# 将容器转换为image并转移到另一台机器上

首先在源机器上要使用`docker ps`查询所有的运行中的容器，找到需要转换为image的容器。接着运行`docker commit <container id> <image  name>`来指定要生成的image的名称。

使用`docker save -o <path/file.tar> <image name>`可以将指定的image提取出来保存到指定的路径下，使用`docker load -i <file.tar>`导入之前保存的镜像。