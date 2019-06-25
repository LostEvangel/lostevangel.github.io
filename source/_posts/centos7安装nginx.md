---
title: CentOS7安装Nginx
date: 2019-06-25 13:31:04
tags: [Nginx]
categories: [Nginx]
photos:
  - "https://github.com/CherryKeinz/cherrykeinz.github.io/blob/master/images/cover/3.jpg?raw=true"
description: CentOS7通過安裝包的方式安裝Nginx
---

# CentOS7安裝Nginx
## 1. 下載nginx安裝包
`wget 官網下載地址`
## 2.解壓編譯安裝
`tar -zxvf nginx-***.tar.gz`

`./configure --prefix=/usr/local/nginx（必须先建好目录）`

此時可能會出現錯誤

`./configure: error: the HTTP rewrite module requires the PCRE library.`

`./configure: error: the HTTP cache module requires md5 functions`

### 2.1  安裝pcre-devel

`yum -y install pcre-devel`

### 2.2 安裝openssl openssl-devel

`yum -y install openssl openssl-devel`

## 3. make、make install

`make`

`make install`

## 4. 防火墻開啟對應端口

`firewall-cmd --zone=public --add-port=80/tcp --permanent`

`firewall-cmd --reload`

