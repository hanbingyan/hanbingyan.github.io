---
layout: wiki
title: Wireshark 入门
categories: Wireshark
description: Wireshark 安装以及简易使用
keywords: Wireshark
---

**目录**

* TOC
{:toc}

### 安装

下面是通过 PPA 源来安装的.

```
sudo add-apt-repository ppa:wireshark-dev/stable
sudo apt-get update
sudo apt-get install wireshark
```

为了安全，要求不是 root 用户也能运行 wireshark，这个在安装的时候会弹出设置界面。还要将自己的用户名添加到 wireshark group 里面， 执行下面的语句，注意将 hby 换成自己的用户名.

```
sudo usermod -a -G wireshark hby
```

### 一些有用的过滤器 Filter

#### 过滤IP地址

```
ip.dst
ip.src
ip.addr == 8.8.8.8
```

#### 过滤协议

```
arp
dns
dns and http
dns or http
```

#### 过滤端口

```
tcp.port == 443
udp.port == 自己选
```

#### 检测TCP问题 package loss

```
tcp.analysis.flags
```

#### 过滤不想看的协议等

```
!(arp or dns or icmp)
```

#### 查看包含某些内容的

```
tcp contains facebook
```

#### 查看HTTP请求/回复

```
http.request
http.response.code == 200
```

#### 其它与安全相关

```
tcp.flags.syn == 1
tcp.flags.reset == 1
sip && rtp
```
