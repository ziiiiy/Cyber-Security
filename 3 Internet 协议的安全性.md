# 3 Internet 协议的安全性

## 3.1 Internet 协议概述

###  TCP/IP 协议族

## 3.2 网际层协议

### IP 协议

- 网际协议 Internet Protocol
- TCP/IP 协议族核心
- 首部 = 20 Bytes + 可变部分
- 安全问题及防护措施

	- 窃听数据报

		- 加密

	- 修改数据报

		- 完整性检测

	- IP 欺骗攻击

		- 源地址鉴别

	- 病态数据报注入包过滤器

		- 防火墙

	- 定向广播攻击

		- 配置路由器禁发此类包

### ARP 协议

- 地址解析协议 Address Resolution Protocol
- 32 bits IP address <--> 48 bits MAC address
- ARP 欺骗攻击，可完全控制主机间流量

	- 配置 IEEE 802.1x 协议，连接交换机时需验证身份
	- 建立静态 ARP 表

### ICMP 协议

- 控制报文协议 Internet Control Message Protocol
- 通知主机到达目的地的最佳路由，报告路由故障
- 安全问题及防护措施

	- ICMP 重定向攻击

		- 使目标机遭受连接劫持和拒绝服务等攻击

	- ICMP路由器发现攻击

		- 不对应答方认证，可能遭受严重的中间人攻击

	- 防火墙穿越攻击

		- 穿越防火墙的访问控制列表和规则集，进而确定该防火墙过滤的内容和具体的过滤方式

## 3.3 传输层协议

### TCP 协议

- 传输控制协议 Transmission Control Protocol
- 面向连接的、可靠的传输层通信协议
- 三次握手

	- C --- SYN = 1, seq = X --> S
	- C <-- SYN = 1, ACK = 1, seq = Y, ack = X + 1--- S
	- C --- ACK = 1, seq = X + 1, ack = Y + 1 --> S

- 安全问题及防护措施

	- SYN Flood 攻击

		- 针对TCP连接建立阶段的三次握手过程
		- 不断向服务器端口发送建立连接请求，使服务器一直处于半开放连接状态，无法接受其他正常连接请求
		- 在服务器前端部署防火墙进行数据包过滤

	- TCP会话劫持攻击

		- 针对TCP协议不对数据包加密和认证漏洞
		- 预测到起始序号，就可以欺骗该目标主机
		- 在 TCP 连接建立时采用随机数作为初始序列号

	- TCP连接建立后的数据阶段攻击

		- 针对TCP的拥塞控制机制的特性
		- 周期性制造网络关键节点拥塞，不断触发拥塞窗 口慢启动过程，降低正常传输能力
		- 网管实时监测网络异常流量

### UDP 协议

- 用户数据报协议 User Datagram Protocol
- 无连接的、不可靠的传输层通信协议
- 特点

	- 无需连接传输高效
	- 无拥塞控制的不可靠交付
	- 数据报首部仅8字节
	- 网络拥塞不降低发送速率

- DoS 攻击

	- UDP Flood 攻击

		- 发送大量 UDP 小包到攻击目标，使其忙于处理和回应 UDP 报文，系统资源使用率飙高，最后不能正常提供服务
		- 如果攻击包是大包，则根据攻击包大小设定包碎片重组大小，通常不小于1500
		- 当攻击端口为业务端口，根据该业务 UDP 最大包长设置 UDP 最大包，过滤异常流量
		- 当攻击端口为非业务端口，设置 UDP 连接规则，要求其首先建立TCP连接

## 3.4 应用层协议

### 概览

- 

### HTTP 协议

- TCP port 80
- 超文本传输协议 Hyper Text Transfer Protocol
- 安全问题

	- 通过网络嗅探工具轻易获得明文的传输数据，从而分析出特定的敏感信息
	- 未对传输内容进行消息完整性检测，可以轻易篡改数据，发动中间人攻击

- 防护措施

	- HTTPS 协议

		- port 443
		- 增加安全层，通过安全套接层 SSL 及传输层安全协议 TLS 实现
		- 攻击方式主要是在 SSL连接还未发生时的中间人攻击

### TENET 协议

- TCP port 23
- 远程登录协议 Teletype Network
- 安全问题及防护措施

	- 通过 Sniffer 记录用户名和口令组合，或者记录整个会话
	- 劫持 Telnet 会话并在认证完成后篡改或插入命令
	- 通过 SSH 对 Telnet 会话加密

### DNS 协议

- UDP 53
- 域名系统 Domain Name System
- 安全问题

	- DNS 欺骗攻击

		- 假冒 DNS 服务器

	- DNS 缓存中毒攻击

		- 给 DNS 服务器注入非法域名地址

	- DNS 重定向攻击

		- 将 DNS 查询重定向到恶意 DNS 服务器

- 防护措施

	- 不要采用基于名称的认证
	- 不把秘密信息放在主机名中
	- 采用 DNSsec 新标准

### SMTP 协议

- TCP port 25
- 简单邮件传输协议 Simple Mail Transfer Protocol
- 安全问题及防护措施

	- 可能成为 DoS 的发源地
	- 邮件别名有时会给黑客提供有用信息
	- “开放中继”允许在任何人之间进行邮件传递
	- 使用 SMTP 认证，并结合加密 SMTP 会话

### FTP 协议

- TCP port 20/21
- 文件传输协议 File Transfer Protocol
- 控制通道

	- 客户机使用 FTP 命令打开通往服务器

- 数据通道

	- 主动模式

		- Client 通过 PORT 命令将随机端口号通知 Server，Server 通过 port 20 建立数据连接

	- 被动模式

		- Client 向 Server 发送 PASV 命令，Server 随机选择 port 并通知 Client，从而建立数据连接

- 安全问题

	- PORT 命令

		- Server 主动对 Client 连接，连接受到攻击时防火墙无法正确处理
		- Client 在 PORT 命令中发送了自己的 IP address 和 port，引起 FTP 反弹攻击

	- Java 程序伪装成 FTP Client 发动攻击
	- 登录口令容易被探测或猜测
	- FTPD 守护程序缺陷

- 防护措施

	- 使用 PASV 命令
	- 然后配置防火墙时禁止所有进入的 TCP 连接
	- 使用密文传输用户名和口令

