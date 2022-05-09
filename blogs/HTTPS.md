---
title: HTTPS知识积累
author: Harrison
date: 2020-08-14 14:23:43
categories:
  - 'Network'
tags:
  - 'HTTPS'
  - 'HTTP'
---
HTTPS相关知识小结，欢迎交流，指正错误。
<!-- more -->

## 1. HTTP协议中所存在的问题

> + 数据是明文传输，容易被窃听截取
> + 数据的完整性未校验，容易被篡改
> + 没有验证对方身份，存在冒充危险

## 2. HTTPS协议

为了解决上述HTTP存在的问题，就用到了HTTPS。

> HTTPS 协议（HyperText Transfer Protocol over Secure Socket Layer）：可以理解为HTTP+SSL/TLS， 即 HTTP 下加入 SSL（Secure Socket Layer，安全套接字层）层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL，用于安全的 HTTP 数据传输。

如下图所示： HTTPS 相比 HTTP 多了一层 SSL/TLS

![HTTPS SSL/TLS](https://gitee.com/yuanlu_k/BlogImages/raw/master/Https/https_ssl.png)

### 2.1. 加密算法

+ 对称加密
  - 即加密的密钥和解密的密钥相同
  - 优点：计算量小、加密速度快、加密效率高。
  - 缺点:（1）交易双方都使用同样密钥，安全性得不到保证；（2）每次使用对称加密算法时，都需要使用其他人不知道的惟一密钥，这会使得发收信息双方所拥有的钥匙数量呈几何级数增长，密钥管理成为负担。

+ 非对称加密
  - 非对称加密将密钥分为公钥和私钥,公钥可以公开,私钥需要保密。
  - 使用公钥对数据进行加密，可以用私钥解密得到数据；也可以使用私钥对数据进行加密，公钥解密得到数据。
  - 但一般使用公钥加密，私钥解密。
  - 优点：非对称加密相比对称加密更加安全
  - 缺点：（1）CPU计算资源消耗非常大；（2）非对称加密算法对加密内容的长度有限制，不能超过公钥长度。比如现在常用的公钥长度是2048位，意味着待加密内容不能超过256个字节。
  
### 2.2. 建立连接

+ HTTP和HTTPS***都需要在建立连接的基础上来进行数据传输***,是基本操作
+ 当客户在浏览器中输入网址的并且按下回车,浏览器会在浏览器DNS缓存,本地DNS缓存,和Hosts中寻找对应的记录,如果没有获取到则会请求DNS服务来获取对应的IP
+ 当获取到IP后,TCP连接会进行三次握手建立连接

### 2.3. TCP的三次握手和四次挥手[](TCP和UDP详解)

过程简图如下所示：

![三次握手和四次挥手](https://gitee.com/yuanlu_k/BlogImages/raw/master/Https/TCP%E8%BF%9E%E6%8E%A5.png)

> ***三次握手（建立连接）***
> + 第一次：建立连接时，客户端发送SYN包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；
> + 第二次：服务器收到SYN包，向客户端返回ACK（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RCVD状态；
> + 第三次：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。
> + 完成三次握手，客户端与服务器开始传送数据，也就是ESTABLISHED状态。
> + 三次握手保证了不会建立无效的连接，从而浪费资源。


> ***四次挥手（释放连接）***
> + 第一次：TCP客户端发送一个FIN，用来关闭客户到服务器的数据传送。
> + 第二次：服务器收到这个FIN，它发回一个ACK，确认序号为收到的序号加1。和SYN一样，一个FIN将占用一个序号。
> + 第三次：服务器关闭客户端的连接，发送一个FIN给客户端。
> + 第四次：客户端发回ACK报文确认，并将确认序号设置为收到序号加1。
  
### 2.4. HTTPS请求
在TCP连接建立之后，HTTPS请求过程如下图所示：

![HTTPS请求](https://gitee.com/yuanlu_k/BlogImages/raw/master/Https/HTTPS.png)

> ①发送客户端支持的加密协议及版本，非对称加密算法，随机数1
> 
> ②服务端筛选加密协议，对称算法，随机数2，证书（License）
> 
> ③客户端先从 CA 验证该证书的合法性，验证通过后取出证书中的服务端公钥
> 
> ④生成随机数3，再用服务端公钥非对称加密随机数3，生成 PreMaster Key
> 
> ⑤服务端依据私钥解密PreMaster Key，得出随机数3
> 
> ⑥至此，客户端和服务端都有了三个随机数，再依据相同的算法生成对称加密的密钥
> 
> ⑦客户端和服务端开始使用对称加密后的密文进行通信。
> 

### 2.5. HTTPS的缺点
+ HTTPS协议多次握手，导致页面的加载时间延长近50%；
+ HTTPS连接缓存不如HTTP高效，会增加数据开销和功耗；
+ 申请SSL证书需要钱，功能越强大的证书费用越高。
+ SSL涉及到的安全算法会消耗 CPU 资源，对服务器资源消耗较大。

## 3. 总结
+ HTTPS是HTTP协议的安全版本，HTTP协议的数据传输是明文的，是不安全的；HTTPS使用了SSL/TLS协议进行了加密处理。
+ HTTPS和HTTP使用连接方式不同，默认端口也不一样，HTTP是80，HTTPS是443。
+ HTTPS中在请求过程中使用非对称加密技术，而在通信过程中使用的是对称加密技术。
+ HTTPS需要非对称加密 、 对称加密 和 CA 共同实现。





