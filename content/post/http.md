---
title: "图解http"
date: 2021-09-13
draft: false
summary: "图解http"
---

# 图解http

## 网络基础TCP/IP

通常使用的网络（包括互联网）是在TCP/IP协议族的基础上运作的。而HTTP属于它内部的一个子集。

计算机与网络设备要相互通信，双方就必须基于相同的方法。
protocol

TCP/IP的分层管理

TCP/IP协议族按层次分别分为以下4层：应用层、传输层、网络层和数据链路层

应用层：应用层决定了向用户提供应用服务时通信的活动。
（TCP/IP协议族内预存了各类通用的应用服务。比如，FTP（文件传输协议）和DNS(域名系统)服务就是其中两类，HTTP协议也处于该层）

传输层：传输层对上层应用层，提供处于网络连接中的两台计算机之间的数据传输
（在传输层有两个性质不同的协议：TCP(传输控制协议)和UDP(用户数据报协议)）

网络层：又名网络互连层，网络层用来处理在网络上流动的数据包。数据包是网络传输的最小数据单位。
该层规定了通过怎样的路径（所谓的传输路线）到达对方计算机，并把数据包传送给对方

与对方计算机之间通过多台计算机或网络设备进行传输时，网络层所起的作用就是在众多的选项内选择一条传输路线。

链路层：又名数据链路层，网络接口层，用来处理连接网络的硬件部分

```
三次握手：用TCP协议把数据包送出去后，TCP不会对传送后的情况置之不理，它一定会向对方确认是否成功送达。
握手过程中使用了TCP的标志 SYN - ACK

DNS：DNS协议提供通过域名查找IP地址，或逆向从IP地址反查域名的服务
```
    
```
HTTP协议的职责：生成针对目标Web服务器的HTTP请求报文
TCP协议的职责：为了方便通信，将HTTP请求报文分割成报文段，按序号分为多个报文段，把每个报文段可靠的传给对方
IP协议的职责：搜索对方的地址，一边中转一边传送
```

```
URI用字符串标识某一互联网资源，而URL标识资源的地点。可见URL是URI的子集
```

```
获取部分内容的范围请求

如果下载过程中遇到网络中断的情况，那就必须重头开始。为了解决上述问题，需要一种
可恢复的机制。所谓恢复是指能从之前下载中断处恢复下载。

要实现该功能需要指定下载的实体范围。像这样，指定范围发送的请求叫做范围请求

Content-Type:multipart/byteranges
```

```
内容协商机制是指客户端和服务器端就响应的资源内容进行交涉，然后提供给客户端
最合适的资源。内容协商会以响应资源的语言、字符集、编码方式等作为判断的基准。

服务器驱动协商
客户端驱动协商
透明协商
```
```
1xx 信息性状态码     接收的请求正在处理
2xx 成功状态码       请求正常处理完毕
3xx 重定向状态码     需要进行附加操作以完成请求
4xx 客户端错误状态码 服务器无法处理请求
5xx 服务器错误状态码 服务器处理请求出错
```
```
200 OK                 表示请求被正常处理了
204 No Content         表示请求处理成功，但没有资源可返回
206 Partial Content    表示客户端进行了范围请求
301 Moved Permanently  永久性重定向
302 Found              临时性重定向
303 See Other          表示由于请求对应的资源存在着另一个URI，应使用GET方法定向获取请求的资源
304 Not Modified       
307 Temporary Redirect    临时重定向
400 Bad Request           表示请求报文中存在语法错误
401 Unauthorized          表示发送的请求需要有通过HTTP认证的认证信息
403 Forbidden             表示对请求资源的访问被服务器拒绝了
404 Not Found             表示服务器上无法找到请求的资源
500 Internal Server Error 表示服务器端在执行请求时发生了错误
503 Service Unavailable   表示服务器暂时处于超负荷或正在进行停机维护，现在无法处理请求
```