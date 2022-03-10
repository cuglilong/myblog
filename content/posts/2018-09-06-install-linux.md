---
title: "Win系统下安装Linux系统所遇到的问题及解决方法"
date: 2018-09-06T09:22:45+08:00
draft: false
tags: [Linux]
comments: true
---
#### 本文主要记录安装Linux（Ubuntu）系统时所遇到的各种奇怪问题以及我所使用的解决方法，此篇将持续更新记录
## 1 引导文件损坏（即进不去系统）
### 1.1 Windos引导文件损坏
#### 我所遇到的情况是：格式化Linux系统盘（准备重装Linux系统）后重启直接进出现
```
grub >
```

#### 我所采用的解决办法是：
##### 1 首先安装好Ubuntu（Ubuntu16.04）系统
##### 2 重启后直接进入Ubuntu系统（自动进入Ubuntu，此时无选择Ubuntu和Windows界面）
##### 3 在Ubuntu系统中修复引导文件（grub），主要参考[KII_2博文](https://www.cnblogs.com/Summer0806/p/6187138.html)
##### 3.1 执行如下命令
```
sudo grub-install /dev/sda
sudo update-grub
```
##### 3.2 重启即可进入系统选择界面，此时即可进入Windows系统
