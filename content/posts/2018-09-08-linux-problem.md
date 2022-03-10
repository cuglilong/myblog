---
title: "Linux（Ubuntu）系统操作常见问题汇总"
date: 2018-09-08T10:08:58+08:00
draft: false
comments: true
tags: [Linux]
---

#### 本文主要记录在使用Linux（Ubuntu）系统时所遇到的问题及解决方法,主要参考[阿里云](https://www.aliyun.com/jiaocheng/129308.html)
## 1 输入`sudo`命令，例如：`sudo apt-get update`，出现如下报错
```
Linux ** is not in the sudoers file.This incident will be reported.
```
### 解决方法：
### 1.1 切换到管理员用户
```
su root
```
### 1.2 修改`sudoers`文件权限
```
chmod 777 /etc/sudoers
```
### 1.3 修改`sudoers`文件
```
vim /etc/sudoers
```
### 在 `root ALL=(ALL:ALL)ALL` 下面添加一行，`yourName` 就是你的用户名：`yourName ALL=(ALL)ALL`
### 然后退出并保存
#### 1.4 改回文件权限
```
chmod 440 /etc/sudoers
```
### 1.5 测试是否解决
```
sudo apt-get update
```
