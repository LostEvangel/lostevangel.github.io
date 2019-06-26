---
title: 搭建ShadowSocks
date: 2018-9-21 18:55:35
tags: [ShadowSocks]
categories: 科学上网
photos:
  - "https://github.com/CherryKeinz/cherrykeinz.github.io/blob/master/images/cover/3.jpg?raw=true"
description: 科学上网必备
---


# 1. 搭建ShadowSocks


## 1.1 客户端

下载
https://shadowsocks.org/en/download/clients.html

![ssclient.png](/image/ssclient.png)

## 1.2 ss的配制与使用

### 1. 检查Python版本

```
$ python -version
```

### 2. 安装m2crypto和python-setuptools

```
$ yum install m2crypto python-setuptools
```

### 3. 安装pip

```
yum -y install epel-release

yum -y install python-pip
```

或者（没试过）
```
$ easy_install pip
```

### 4. 安装ss

```
$ pip install shadowsocks
```

### 5. 配置服务器参数

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

### 6. 安装gevent

Gevent可以提高ss性能，由于gevent依赖于libevent和greenlet

```
$ yum install -y libevent
$ pip install greenlet
$ pip install gevent
```

### 7. 配置防火墙

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
##  1.3 查看修改ss服务器的端口密码

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

```shell
ssserver -c /etc/shadowsocks.json -d start

ssserver -c /etc/shadowsocks.json -d stop

ssserver -c /etc/shadowsocks.json -d restart
```

参考：
https://github.com/easonhuang123/blog/issues/1
https://blog.whsir.com/post-1045.html

# 2. 搭建SSR

## 2.1 服务器

### 1.install

1. install git
2. git clone https://github.com/shadowsocksr/shadowsocksr.git （github ssr）

### 2.Usage for single user on linux platform

If you clone it into "~~/shadowsocksr"move to "~~/shadowsocksr", then run:

```shell
bash initcfg.sh
```

move to "~/shadowsocksr/shadowsocks", then run:

```shell
python server.py -p 443 -k password -m aes-128-cfb -O auth_aes128_md5 -o tls1.2_ticket_auth_compatible
```

Check all the options via `-h`.

You can also use a configuration file instead (recommend), move to "~~/shadowsocksr" and edit the file "user-config.json", then move to "~~/shadowsocksr/shadowsocks" again, just run:

```shell
python server.py
```

To run in the background:

```shell
./logrun.sh
```

To stop:

```
./stop.sh
```

To monitor the log:

```
./tail.sh
```

### 3.配置firewall，开放端口 （for centos 7）

```shell
[root@centos7 ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent
[root@centos7 ~]# firewall-cmd --reload　# 重启防火墙
```

## 2.2 客户端

github ssr

# 3. 关于firewall

<https://www.cnblogs.com/Sungeek/p/8257681.html>

当下面实验修改的是永久生效的策略记录时，必须执行"**--reload**"参数后才能立即生效，否则要重启后再生效。

**查看当前的区域：**

```shell
[root@CentOS7 ~]# firewall-cmd --get-default-zone
public
```

**查询网卡的区域：**

```shell
[root@Centos7 ~]# firewall-cmd --get-zone-of-interface=eno16777736
public
```

**在public中分别查询ssh与http服务是否被允许：**

```shell
[root@Centos7 ~]# firewall-cmd --zone=public --query-service=ssh
yes
[root@Centos7 ~]# firewall-cmd --zone=public --query-service=http
no
```

**实例A:允许https服务流量通过public区域，要求立即生效且永久有效：**

　　**方法一:分别设置当前生效与永久有效的规则记录：**

```shell
[root@Centos7 ~]# firewall-cmd --zone=public --add-service=https
success
[root@Centos7 ~]# firewall-cmd --permanent --zone=public --add-service=https
success
```

　　**方法二:设置永久生效的规则记录后读取记录：**

```shell
[root@Centos7 ~]# firewall-cmd --permanent --zone=public --add-service=https
success
[root@Centos7 ~]# firewall-cmd --reload
success
```

**实例B:不再允许http服务流量通过public区域，要求立即生效且永久生效：**

```shell
[root@Centos7 ~]# firewall-cmd --permanent --zone=public --remove-service=http
success

[root@Centos7 ~]# firewall-cmd --reload 
success
```

**实例C:允许8080与8081端口流量通过public区域，立即生效且永久生效：**

```shell
[root@Centos7 ~]# firewall-cmd --permanent --zone=public --add-port=8080-8081/tcp
success
[root@Centos7 ~]# firewall-cmd --reload 
success
```

**实例D:查看模拟实验中的规则：**

```shell
[root@Centos7 ~]# firewall-cmd --zone=public --list-services 
dhcpv6-client http https ssh
[root@Centos7 ~]# firewall-cmd --zone=public --list-ports 
8080-8081/tcp
```

**实例E：将访问主机888端口的请求转发至22端口：**

```shell
[root@Centos7 ~]# firewall-cmd --permanent --zone=public --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.0.100
success
[root@Centos7 ~]# firewall-cmd --reload 
success
```

例如安装Nagios后，要开放5666端口与服务器连接，命令如下

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```shell
[root@centos7 ~]# firewall-cmd --add-port=5666/tcp　即时打开，这里也可以是一个端口范围，如1000-2000/tcp
success
[root@centos7 ~]# firewall-cmd --permanent --add-port=5666/tcp　写入配置文件
success
[root@centos7 ~]# firewall-cmd --reload　重启防火墙
success
[root@centos7 ~]#
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**CentOS 7快速开放端口：**

CentOS升级到7之后，发现无法使用iptables控制Linuxs的端口，baidu之后发现Centos 7使用firewalld代替了原来的iptables。下面记录如何使用firewalld开放Linux端口：

**开启端口**

```shell
[root@centos7 ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent
```

**查询端口号80 是否开启：**

 

```shell
[root@centos7 ~]# firewall-cmd --query-port=80/tcp
```

**查询有哪些端口是开启的:**

 

```shell
[root@centos7 ~]# firewall-cmd --list-port
```

**![img](https://images2018.cnblogs.com/blog/872610/201806/872610-20180607143807700-372634451.png)**

**命令含义：**

--zone #作用域
--add-port=80/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

**重启防火墙**

```shell
[root@centos7 ~]# firewall-cmd --reload
```