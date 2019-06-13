---
title: 搭建ShadowSocks
date: 2018-9-21 18:55:35
tags: [ShadowSocks]
categories: 科学上网
description: 科学上网必备
---


# 搭建ShadowSocks


### 客户端

下载
https://shadowsocks.org/en/download/clients.html

![ssclient.png](/image/ssclient.png)

### ss的配制与使用

1. 检查Python版本

```
$ python -version
```

2. 安装m2crypto和python-setuptools

```
$ yum install m2crypto python-setuptools
```

3. 安装pip

```
yum -y install epel-release

yum -y install python-pip
```

或者（没试过）
```
$ easy_install pip
```

4. 安装ss

```
$ pip install shadowsocks
```

5. 配置服务器参数

```
$ vi /etc/shadowsocks.json
```

```
{                                  
    "server":"my_server_ip",          //服务器的IP地址
    "server_port":7711,              //服务器的端口
    "local_address": "127.0.0.1",       //本机IP地址
    "local_port":1080,               //本机端口
    "password":"mypassword",        //自己设定的密码
    "timeout":300,                  //超出时间
    "method":"aes-256-cfb",          //加密方法，推荐使用"aes-256-cfb"
    "fast_open": false                //true 或 false}
}
```

若是多用户模式，将server_port和password合并为port_password：

```
"port_password": {
         "443": " mypassword 1”,  //对应端口设定不同的密码
         "8888": " mypassword 2”
     },
```

6. 安装gevent

    Gevent可以提高ss性能，由于gevent依赖于libevent和greenlet

```
$ yum install -y libevent
$ pip install greenlet
$ pip install gevent
```

7. 配置防火墙

    为了提高系统的安全性，需要安装防火墙

```
$ yum install firewalld
```

启动防火墙

```
$ systemctl start firewalld
```

开启相应端口

```
$ firewall-cmd --permanent --zone=public --add-port=443/tcp
$ firewall-cmd --reload
```
###  查看修改ss服务器的端口密码

- 查看当前ss服务器所开放的端口

```
ss -lntp | grep ssserver
```

- 查看当前ss服务器的密码，通过以下命令可见ss的配置文件，配置文件中自然有密码

```
ps aux | grep ssserver
```

- 用cat查看下配置文件即可看见密码了，vim修改

```
vim /etc/shadowsocks.json
```
- ss启动停止方法

启动，停止，重启，状态：

```
ssserver -c /etc/shadowsocks.json -d start

ssserver -c /etc/shadowsocks.json -d stop

ssserver -c /etc/shadowsocks.json -d restart
```

参考：
https://github.com/easonhuang123/blog/issues/1
https://blog.whsir.com/post-1045.html