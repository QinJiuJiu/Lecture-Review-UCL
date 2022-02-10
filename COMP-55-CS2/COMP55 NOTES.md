# COMP55 NOTES

[toc]

## Week1

### TCP/IP overview

![The TCP:IP Stack of Protocols](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/The TCP:IP Stack of Protocols.png)

![TCP:IP image](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/TCP:IP image.png)

### IPv4 overview

#### IPv4数据包格式

![IPv4 packet](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/IPv4 packet.png)

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

  ![Transport Mode](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/Transport Mode.png)

* Tunnel Mode

  在源数据包外新建一个数据包头，可以隐藏源IP地址。

  ![Tunnel Mode](/Users/zyt/Documents/zyt-uk/term2/comp55-CS2/image/Tunnel Mode.png)

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



DNS参考链接：https://zhuanlan.zhihu.com/p/144183767
