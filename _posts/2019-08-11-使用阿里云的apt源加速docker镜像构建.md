---
layout: post
title:  "使用国内软件源镜像加速docker镜像构建"
date:   2019-08-11 21:03:36 +0530
categories: OS
tags: [docker]
---

## 替换默认软件源
1. 将ubuntu官方软件源替换为阿里云提供的软件源镜像

2. 设置环境变量避免交互导致`apt-get intstall`失败

具体dockerfile可加入如下命令

```sh
RUN  sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
     && apt-get clean
     && DEBIAN_FRONTEND=noninteractive \
     && apt-get update -y
```

`apt-get update`后，替换为了阿里云的软件源

![aliyun-docker](https://phaedo.github.io/blog/post-assets/2019-08/aliyun-docker.png)
