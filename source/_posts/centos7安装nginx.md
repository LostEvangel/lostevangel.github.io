---
title: CentOS7安装Nginx
date: 2019-06-25 13:31:04
tags: [Nginx]
categories: [Nginx]
photos:
  - "https://github.com/LostEvangel/LostEvangel.github.io/blob/master/image/cover/3.jpg?raw=true"
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

# 为nginx添加SSL支持模块

使用`proxy_pass https://XXXXXXXXX`时，遇到问题

`nginx https protocol requires SSL support in /usr/local/nginx/conf/nginx.conf `

**原因**：在当初编译安装时，没有加`--with-http_ssl_module`参数。

**解决办法**：重新编译源码包。

关闭nginx，切到源码包目录

```shell
./configure --prefix= /usr/local/nginx --with-http_ssl_module
make
```

<font color='red'>注意，此时千万不能手快直接 make install！</font>

```shell
cp /usr/local/nginx/sbin/nginx ~/
cp objs /nginx /usr/local/nginx/sbin/
```

其中，objs/nginx 是新编译出来的nginx可执行程序。
覆盖之后，再尝试重新启动nginx，就没有问题了。