---
title: Shadowsocks服务器搭建
date: 2018-12-22 17:18:42
tags: 翻墙
comments: true
---

<!-- toc -->

[TOC]

shadowsocks官网：https://shadowsocks.org
# server客户端安装
官网提供了各种安装方案：https://shadowsocks.org/en/download/servers.html，
这里选择pip安装。

## 安装shadowsocks
```
pip install shadowsocks
```
安装后会在 /usr/bin 下安装两个程序：ssserver 和 sslocal。

使用命令`ssserver -h` 可以查看帮助信息。

## 配置文件
新建文件/etc/shadowsocks.json，写入如下内容
```
{
    "server":"0.0.0.0",
    "server_port":8918,//自定义，建议使用443端口，其它端口容易被查封
    "password":"xxx",//自定义
    "timeout":60,
    "method":"aes-256-cfb",
    "workers": 4,
    "fast_open": true
}
```
## 运行
```
$ ssserver -c /etc/shadowsocks.json --log-file=/log/shadowsocks/ss.log -d start //启动
$ ssserver -c /etc/shadowsocks.json -d stop  //停止
```
## 开机自启动
编辑 `/etc/rc.local`,在其中将ss的启动命令`ssserver -c /etc/shadowsocks.json --log-file=/log/shadowsocks/ss.log -d start`添加进去，就能开机自启动了

## 可能出现问题
### 防火墙问题
如果成功运行，但是无法连接，可能是防火墙设置问题。

- 可以选择添加当前端口到白名单或关闭防火墙解决。以我使用的 Ubuntu 为例。
```
$ iptables -I INPUT -4 -p tcp -m tcp --dport 8918 -j ACCEPT
$ service iptables save
```
- 如果使用的是云服务器，需要在云服务器的控制台-防火墙中添加规则开放端口。

### Cannot assign requested address
如果出现报错: `Cannot assign requested address`, 请将 `/etc/shadowsocks.json`中的 `server` 换成 `0.0.0.0`, 然后重新启动上面的命令。