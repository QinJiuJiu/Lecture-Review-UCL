# COMP55 NOTES

[toc]

## Week1

### TCP/IP overview

![The TCP:IP Stack of Protocols](image/The TCP:IP Stack of Protocols.png)

![TCP:IP image](image/TCP:IP image.png)

### IPv4 overview

#### IPv4数据包格式

![IPv4 packet](image/IPv4 packet.png)

#### Traceroute

Traceroute (Windows 系统下是tracert) 命令利用ICMP 协议定位计算机和目标计算机之间的所有路由器。Routers decrement the TTL by one every time they route a datagram. If TTL reaches 0, the datagram is discarded and a “ICMP Time Exceeded” packet is sent to the sender.

⚠️Some hosts may block packets needed for traceroute to work.

**MPLS protocol**: 用标签转发，而不是进行IP路由表的查找

**Ping of Death**: Lower layer protocols may be unable to transmit packets as large as the IP packet size (65,535 bytes). For this reason, packets can be split in multiple fragments. ➡️ Problem: the recipient operating system has to receive all fragments before it can reassemble them – the total datagram length is not known a priori. ➡️ Total length can be higher than 65,535 bytes and can overflow a static buffer：

```
ping –l 65510 192.168.0.123
```

### ICMP

是TCP/IP协议簇的一个子协议，用于在IP主机、路由器之间传递控制消息。控制消息是指网络通不通、主机是否可达、路由是否可用等网络本身的消息。

### NAT

NAT shares the same IP address between several hosts to deal with IPv4 shortages.

****

**同一个网络下**的IP数据包转发不需要路由器，用MAC地址在数据链路层进行转发。Hosts can sniff traffic on their network segment by entering “promiscuous mode”. 

### MAC

MAC地址（Media Access Control Address），也称为局域网地址（LAN Address），MAC位址，以太网地址（Ethernet Address）或物理地址（Physical Address）。在OSI模型中，第三层网络层负责IP地址，第二层数据链路层则负责MAC位址。MAC地址用于在网络中唯一标示一个网卡，一台设备若有一或多个网卡，则每个网卡都需要并会有一个唯一的MAC地址。

### ARP (IP和MAC的对应关系)

The Address Resolution Protocol (ARP) is used by hosts to know which MAC address is associated to which IP address on the local link.

<font color='red'>【问题】</font> Issues with ARP Spoofing

Race condition: legitimate ARP replies might restore the IP / MAC mapping. Attackers continuously send spoofed ARP replies to keep the fake mapping.

<font color='green'>【解决方法】</font> Switched Ethernet

The switch keeps a mapping of which host (MAC address) is connected to which port.

这个可以解决sniff的问题

<font color='red'>【仍存在问题】</font>

* ARP spoofing is still a problem: attackers can make the switch believe that a host is connected to a different port.
* MAC flooding: MAC / port mappings are stored in a table, if the table is filled some switches will start forwarding all traffic to all ports.（在典型的MAC flooding中，攻击者能让目标网络中的交换机不断泛洪大量不同源MAC地址的数据包，导致交换机内存不足以存放正确的MAC地址和物理端口号相对应的关系表。如果攻击成功，交换机会进入failopen模式，所有新进入交换机的数据包会不经过交换机处理直接广播到所有的端口（类似HUB集线器的功能）。）

****

**不同网络下**数据包转发需要路由。

### BGP

The Border Gateway Protocol (BGP) is the protocol used by Internet Service Providers to tell each other which IP addresses they own.

Different networks on the Internet are called **Autonomous Systems (ASes)**.

<font color='red'>【存在问题】</font>

BGP advertisements are not authenticated so can be spoofed, resulting in route hijacking. 利用漏洞 ➡️ If two routes conflict, the one that covers the smaller IP space wins. Attackers advertise multiple small advertise.

<font color='green'>【解决方法】</font>

* Authenticating BGP routes – Resource Public Key Infrastructure (RPKI) 

* Having routers decide if an advertised route looks anomalous[异常的] (arms race)

****

### IPSec

 IPSec can authenticate and encrypt every IP packet（验证header，加密报文）

* Security Association (**SA**): defined before communication is started (between endpoints) – specifies algorithms used for authentication and encryption, and exchanges keys through the IPSec Key Exchange (IKE)
* Authentication Header (**AH**): extension to IP that provides authentication and integrity of datagrams, plus a timestamp prevents replay attacks
* Encapsulating Security Payload (**ESP**): extension to IP that provides authentication, integrity, and confidentiality (encryption)

两种模式：

* Transport Mode

  直接加在源数据包中间。不支持NAT：因为如果采用 Transport 连接一端是网关的时候，网关有 NAT 功能，会将地址变换，而Transport Mode是只识别原 IP 的，这样就会被直接丢弃掉。

  ![Transport Mode](image/Transport Mode.png)

* Tunnel Mode

  在源数据包外新建一个数据包头，可以隐藏源IP地址。

  ![Tunnel Mode](image/Tunnel Mode.png)

### IPv6

IPv4 addresses are 32 bit long

IPv6 addresses are 128 bits long

<font color='red'>【存在问题】</font>

* 技术不是很成熟，存在一定bug
* IPv6可以绕过为IPv4设计的控制
* 没有NAT存在隐患

<font color='green'>【存在优势】</font>

* scanning IPv6 is hard.
* Autoconfiguration used to include MAC address in IP address, which has privacy implications.



## Week 2

### UDP(User Datagram Protocol )

在IP上增加了端口号（port number），通常丢几个包不要紧，所以用来传输视频或语音。

**使用UDP传输的协议**

* Domain Name System (DNS, port 53)

  但是DNS查询超过512字节的时候，使用TCP传输，因为TCP可以分fragment传输。

* Dynamic Host Configuration Protocol (DHCP, ports 67, 68)

* Hypertext Transport Protocol HTTP/3 (port 443) 

  因为这个使用了额外的QUIC保障传输

* µTP (BitTorrent, multiple ports) 

  网络超载时会自行降低速度

**UDP spoofing**

local network：UDP spoofing / hijacking（攻击者网络嗅探到request包，然后在服务器发送UDP数据包之前发送伪造的UDP reply 数据包到目标）

remote target：blind UDP spoofing（看不到content，猜测request在什么时候）

**UDP portscan**

发送空的UDP数据包到每个端口，如果端口没开放，会返回ICMP。用这种方法来猜测应用程序可能在哪些端口上运行。

<font color='green'>【应对portscan】</font>：一些操作系统限制发送ICMP包的频率

Sweeping：扫描多个hosts的某个相同的端口（for example to see which hosts on a network are running a webserver）

### TCP

面向连接的服务，不会丢包，网络拥堵时自动降速，correct ordering segments

source port is randomly picked by the client, destination port is the one the target service is listening on.

**使用TCP传输的协议**

* Hypertext Transport Protocol HTTP/0,1,2 (port 80)，HTTPS port 443

*  Telnet – port 23, Rlogin – port 513（source port）

  不加密的远程连接到别的电脑上

* Secure Shell (SSH) – port 22

  加密连接

* Simple Mail Transfer Protocol (SMTP) – port 25

  邮件系统

* Internet Message Access Protocol (IMAP) – port 143

  Use for clients to access a mailbox stored on a mail server

**TCP 数据包解析**

* Sequence number：每个segment的位置
* Acknowledgement number：最后一个数据包的位置
* Window: 只接受在window范围内的segments
* Checksum：数据包完整性
* flags
  * SYN：请求连接，想要得到ack回复
  * ACK：确认收到了segment
  * FIN：请求关闭连接（不强制）
  * RST：强制关闭连接
  * PSH：requests to pass the data stream to the user level (program) as soon as possible
  * URG：data is urgent

**SET UP TCP connections**

![Set up TCP connection](image/Set up TCP connection.png)

**TRANSFER TCP data**

![Transfer TCP data](image/Transfer TCP data.png)

**CLOSE TCP connection**

![Close TCP connection](image/Close TCP connection.png)

### TCP Security

**TCP spoofing**：sequence number使得TCP spoofing变得困难。

sequence number不能太随机：Purely random sequence numbers is ideal for security, but could lead to confusion if two connections end up with sequence numbers that are close to each other.

**TCP Hijacking**：攻击者向server发送数据包，server向用户发送ack数据包，但是sequence number没有办法对应上，an infinite loop of segments is generated – ACK storm。某种情况下丢包了会使网络重新安静下来，但攻击者可以继续发送数据包，造成下一次ACK storm。

⚠️original Linux TCP implementation中TCP数据包在某个范围区间都会被接纳——blind RST attacks and data injection attacks。RFC 5961 reduces the window of acceptable segments，如果数据包不在这个区间，会发送一个challenge ACK。攻击者可以利用这些实现测信道攻击以猜测sequence number。

**TCP portscan类型**

* connect() scan：三次握手建立完整连接

  if the handshake is successful the port is open

* SYN scan：半开放连接，发送 SYN

  open：SYN + ACK segment

  close：RST

* FIN scan：发送FIN

  open：ignore

  close：RST

* XMAS scan：segments with FIN, PSH, URG flags set

*   Null scan：segments with no flags are sent

* **Idle scanning（利用第三方实现攻击，更加隐蔽）**

  Step 1: 攻击者发送SYN+ACK，获得idle的ipid1（RST包，因为僵尸主机发现非法响应，所以发送RST数据包）

  Step 2: 攻击者发送SYN到目标地址（假冒idle发的）

  Step 3: 攻击者发送SYN+ACK，获得idle的ipid2

  open：ipid2=ipid1+2

  close：ipid2=ipid1+1

同时，扫描还能获得一些其他的信息（week2 PPT page.68）

**Initial sequence numbers的随机性质**

生成算法，每段时间rekey，保证前十五位不会相同，只有后十五位是真随机

![initial sequence number](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/initial sequence number.png)

### DNS and TLS security

**DNS**

用来匹配域名和具体ip地址

两种DNS servers：

* Recursive servers
* Authoritative servers：存了IP-domain的映射

The DNS system is organized as a hierarchical tree: root servers → top level domain servers → second level domain servers etc.

![DNS graph](image/DNS graph.png)

大多数情况下，root server会接受太多请求，可能会有网络用塞。所以设置cache存储一些records（在recursive serves上，一次请求会返回请求对DNS地址，同时还会返回其他一些DNS地址，下次请求会直接在这些地址里找），就不用request整个DNS层次，同时还能确保安全（因为顶层server看不见用户的请求信息）。

**DNS 安全性**

<font color='red'>【存在问题1】</font>

DNS基于UDP，所以也可能遭受UDP相关的攻击。eg. spoof DNS reply可以将其他domain redirect到自己的IP address上。

<font color='green'>【解决方案1】</font>

DNS requests include a 16-bit ID that identifies them （对local network没用，但是对remote network有用）

<font color='red'>【存在问题2】</font>

cache poisoning：在cache里存储相关DNS record时，不检查这个reponse的来源。攻击者可以植入假的DNS record。

<font color='green'>【解决方案1】</font>

DNS clients must ensure that DNS responses are relative to the domain that they queried.

**Kaminsky漏洞**：攻击者向Recursive servers发送不存在域的DNS查询，然后，解析程序将查询转发到Authoritative servers，以获取错误domain的IP地址。此时，攻击者向Recursive servers注入了大量伪造的响应，希望这些伪造之一与原始查询的16-bit ID相匹配。如果攻击成功，则攻击者已使用伪造IP地址毒害了Recursive servers的DNS缓存。直到TTL为止，请求这一domain的人都将获得伪造的IP地址。

能成功的原因：birthday paradox（If a random function yields results in a range H with equal probability, after 1.25 $\sqrt𝐻$ elements there is a 50% chance that two of the previously observed elements were the same）

DNS参考链接：https://zhuanlan.zhihu.com/p/144183767

**DNS 安全措施**

* 随机source port
* 随机域名的大小写
* DNSSEC 加密验证

****

**TLS**

用公钥加密进行验证，用certificate商订一个对称加密密钥。

Advantage：prevents spoofing and hijacking，因为密钥只能用私钥解密。

Disadvantage：如果certificates没有验证的话很容易遭到MITM攻击；攻击者可以在browser中插入假的CA certificate。
