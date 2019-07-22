---
layout: post
title:  "zsh环境下如何补全docker命令"
date:   2019-07-18 21:03:36 +0530
categories: Bash
tags: [zsh, docker]
---

1. 确定你使用了oh-my-zsh的shell环境；（如果没有安装的话，推荐使用，官方地址：https://ohmyz.sh/）

2. 打开/zshrc文件  
```bash
vim ~/.zshrc
```

3. 找到插件配置项`plugins=()`，增加`docker docker-compose`两个插件  
![zshrc-docker-img](https://phaedo.github.io/blog/post-assets/2019-07/zshrc-docker.png)

4. 启动以后，docker命令就能补全了
```bash
source ~/.zshrc
```
