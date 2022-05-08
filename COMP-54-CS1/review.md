## COMP0054 Lecture Review

[TOC]

## Lecture 1 Principles

Design security system $\rightarrow$ Define security system (Meet security policy) $\rightarrow$ Define security policy (Create threat model)

### 1.1 Introduction and Ethics

Code of conduct 

### 1.2 What is Security?

Safety vs. Security

* Safety：抵抗accident，抵御nature，可根据历史数据建模
* Security：抵抗intentional攻击，抵抗intelligent beings，要对攻击者的策略进行建模

Implications of Digital Information：复制容易；传播快；存储方便；来源难查

**CIA Triangle**

![CIA Triangle](image/CIA Triangle.png)

eg. Mass surveillance监控(C&I);spam(I&A);data breach(C因为没有修改data)

**Binary Model & Risk Management Model**

* Binary Model：secure & insecure（这个边界很难找）
* Risk Management Model：没有完全的安全和不安全

The Security Mindset：ant的例子（可以给任何人邮寄ants）

### 1.3 Threat Modelling

* Threats （攻击者是谁）$\rightarrow$ Attacker-centric

* Vulnerabilities （系统的漏洞在哪）$\rightarrow$ System-centric

* Likelihood attack（发生的可能性）

* Impact （attack发生后的影响）$\rightarrow$ Asset-centric

* Protection （保护措施的cost）

A system is “secure” if an adversary constrained by a **specific threat model** cannot violate the **security policy**.

**Security mechanism**: Technical mechanism used to ensure that the security policy is not violated by an adversary operating within the threat model.

### 1.4 Threat Modelling: Some Examples

* threats（capabilities&motivation）人和动机
* vulnerabilities（capabilities&vulnerabilities）
* likelihood（motivation&capabilities&vulnerabilities分析发生可能性）
* impact（motivation&scale）
* protection（vulnerabilities&cost）

### 1.5 Design Principles [SS’73]

* Least privilege 分析用户需要多少权限 eg.linux文件权限分配

* Separation of responsibilities 分层设计，不同人员管理不同内容

* Complete mediation 多次检查权限的更新，保持功能性

* Fail-safe default eg.accessing outside internet白名单而不是黑名单

* Defence in depth 安全保护的程度

* Open design eg.让密钥保密而不是加密算法保密

* Psychological acceptability 设计尽量实用

* Economy of mechanisms 简单

**Trusted computing base (TCB)** refers to every component of the system upon which the security policy relies (could be hardware, software, etc.) if something goes wrong then the security policy may be violated

Security + security ≠ security（eg.每解决一个bug可能产生更多新的bug，安全策略的融合可能并不安全）

### 1.6 Design Principles: Computer Architecture

Code is data：代码和数据一起存储，计算性能提升但是容易被恶意利用（eg.bof）

**Abstraction:** specifying meaning and behaviour of software while hiding implementation details 

**Modular code** exploits abstraction and enables composition and reuse

**Layers**

* Pros：更好的并存；可以忽略被其他层解决的问题

* Cons：上层难以解决底层问题；漏洞可能传播；单层缺陷会影响上层

**Moore’s Law**：circuit density增加；computer performance roughly doubles every 18-24 months

**Halting Problem**：anything sufficiently interesting cannot be determined about a program $\rightarrow$ Security can never be solved!



## Lecture 2 Access Control

### 2.1 Access Control: Introduction

Access Control：security mechanism that ensures all accesses and actions on system objects by principals are within the security policy.

三个对象：people，resources，functions

三块内容：authentication，authorization，audit

**Mandatory vs. Discretionary Access Control**

* Mandatory Access Control

  安全政策规定权限，用于需要中心化管理的组织

* Discretionary Access Control

  All objects have owners，owners自己决定权限赋予，<u>Note: there is still a security policy! DAC is a mechanism</u>

**Access Control Matrix**

三个元素：

* O: Objects

* S: subset of objects called subjects

* R: set of access rights

Reference Monitor：系统中强制执行访问控制决策的部分

### 2.2 Bell-LaPadula vs. Biba Models

Each document has a level. Each person has a clearance.

文件的四个安全级别：Top Secret > Secret > Confidential > Unclassified

**Bell LaPadula Model**

Rules: Read Down, Write Up

Dominance relationship: A level (c1, l1) “dominates” (c2, l2) iff c1 ≥ c2 and l2 is a subset of l1

Clearance：“Clearance” of a subject is the maximum level it has been assigned.

Principles of Integrity Mechanisms

* Separation of duties：执行决策时需要多方的认可。（交易记录的双方和这些记录都必须匹配；发射导弹所需的两名官员。）
* Rotation of duties：只允许委托人在任何特定角色上有有限的时间，限制在此角色中的其他行动
* Secure logging：日志一致性

**Biba Model**

Rules: No Read Down, No Write Up

****

<u>Bell LaPadua ensures confidentiality</u>

<u>Biba ensures integrity</u>

Sanitization problem：Process of taking objects with “low” integrity and “lifting them” to “high integrity”，如Web服务器（高）接受来自用户（低）的输入，然后将其传递给SQL解释器

解决方案：“基于permission而不是exclusion的基本访问决策”；在将其完整性提升到“高”之前，积极验证“低”对象是否在一个有效的集合内。

### 2.3 Graham-Denning Model

Each object has an “owner”

Each subject has a “controller”

A right may be transferable (with *) or not.

owner可以进行授权

### 2.4 Access Control: Implementation Issues

<u>How to “store” the Access Control Matrix?</u>

以人为单位存储 / 以资源为单位存储

<u>How to keep rules fine grained?</u>

mac：针对各个软件都有权限控制

**The Confused Deputy Problem**

Alice (OS user) asks Bob (OS server) to read a file (exam.pdf) and give her the content nicely formatted. 在具有环境权威的系统中，很难表示一个行动正在“代表”他人发生

解决方案：在Bob的流程中重新实现访问控制 / 允许Bob检查对Alice的授权

### 2.5 Role-Based and Decentralised Access Control

**Role-Based Access Control**

can implement MAC or DAC large hierarchical organisations

存在问题：

* Role Explosion：难以建立fine grained roles
* limited expressiveness：problem with implementing least privilege
* Separation of duty policies：
* example policy: any two doctors can authorise a procedure. but, how to ensure that they are distinct!

**Distributed Access Control**

不同的实体可以执行授权；不同的实体可以控制策略；分散访问决策；没有中央管理

Certificates and Electronic Signatures：

* 生成密钥对：生成一个签名密钥和一个验证密钥
* 签名：使用一个签名密钥，Alice可以“签名”一个消息M来生成S
* 验证：使用验证密钥和S，任何人都可以验证Alice签名M
* 安全属性：在不知道签名密钥的情况下，不能产生通过“验证”的签名。

K A -> B means: “K says A if B”

![DAC signature](image/DAC signature.png)

**Audit Logs**：Keep records of every operation taken and decision made against security policy.

### 2.6 Design Principles: Access Control

* Least privilege 每个人分配够使用的最小权限

* Separation of responsibilities 🈚️

* Complete mediation 多次检查reference monitor

* Fail-safe default eg.accessing outside internet白名单而不是黑名单 root权限

* Defence in depth 🈚️

* Open design eg.让密钥保密而不是加密算法保密

* Psychological acceptability 设计尽量实用

* Economy of mechanisms 简单

## Lecture 3 Auth/Passwords/Biometrics

### 3.1 Authentication

Authentication vs. Access Control

* Authentication：确保用户是具有某一类权限的用户群体

* Access Control：确保用户权限符合security policy

Authentication三个方面：What you know；What you have；What you are

----------------------------------------What you know----------------------------------------

**Passwords**

hard to leak/steal? ➡️  Hash Function

Hash Function：把输入变成固定长度；One-way function（Given x, calculating f(x) is easy. Given f(x), finding x is hard.）

好的hash function：

* flip one bit on input and half bits flip on output

* “No” collisions

存储密码时使用hash function，服务端就无法知道具体的密码

### 3.2 Breaking Passwords

linux /etc/shadow存储每个用户密码的hash值

**Offline vs Online Attacks**

Online:

* eg website login

Offline:

* eg stealing /etc/shadow

Password Cracking：彩虹表（存储常用密码的哈希值，字典攻击），**哈希值一样的密码一样**

解决方案：在密码里加salt -> uname,s,H(pword||s)，可以让字典攻击失败

<u>how to retrieve hashed and salted password?</u>

easy to enter?retrievable? ➡️  Graphical Passwords

easy to memorise? ➡️  安全问题

存在矛盾：密码安全性 vs 密码可用性

### 3.3 Challenge/Response

----------------------------------------What you have----------------------------------------

**challenge-response**

![challenge-response](image/challenge-response.png)

下图是错误的，因为攻击者可以伪造MAC

![wrong-c-r](image/wrong-c-r.png)

**Two-way challenge-response**

![2way challenge-response](image/2way challenge-response.png)

下图是错误的：

![wrong-2way-cr1](image/wrong-2way-cr1.png)

错误原因：攻击者可以构建信息发送给B以获得需要发送的Enc内容

![wrong-2way-cr2](image/wrong-2way-cr2.png)

**One-Time Passwords**

根据专门算法、每隔60秒生成一个不可预测的随机数字组合

**pre-image resistance**: given h, hard to find m such that H(m) = h

----------------------------------------What you are----------------------------------------

Biometrics：Fingerprint Scanners；Hand Scanners；Retina Scanners

四个考虑维度：

* usable?

* maintainable?
* how close is the match?
* feature stability?

CAPTCHA：图形验证码

****

上述三个维度（What you know；What you have；What you are）组合使用安全性更高

## Lecture 4 Physical/No Tech Hacking

### 4.1 Security Psychology

### 4.2-4.3 Deterrence & Physical barriers

Entry control cycle：

* Deter：威慑

  三个维度的措施：监控程度、目标强化措施、环境管理措施

  eg. 军备力量威慑

  SS：**Fail-safe default** & **Defence in depth** 

* Detect 

  Access Control

* Alarm 

* Delay 

* Respond

### 4.4 Social Engineering

社工，钓鱼

## Lecture 5 Hardware

### 5.1 Physical Security and Counterfeits

CIA中的**Integrity**

Tamper-proof Software：Post hashes of files to download；Repeatedly check own file hashes；Certificates（很多盗版软件会做假证书）!

### 5.2 Hardware Security Intro

错误解决方案：

*  Check that the program is not doing something bad： Fundamental Principle of Sanitization，高级别接受低级别的用户输入。
* Check that the program is doing only something good：莱斯定理，是NP完全问题，没有解决方案。

可用解决方案：

* Emulation：仿真所有程序，确定其没有修改ref monitor
* Virtualization：动态检查不安全的程序部分
* Typing：确保程序只对“允许的”内存区域进行写入

### 5.3 Memory Protection and Security

**MULTICS operating system (1970s).** 

![multics](image/multics.png)

**Virtual Memory**

（操作系统里的）虚拟线性地址映射TLB

Memory Pages and Security：页表

Access Control to Memory Pages：supervisor和user

### 5.4 Hardware Security Attacks and Countermeasures

讨论虚拟地址映射的问题

Direct Memory Access (DMA)：确保用户不能直接访问物理地址。DMA 传输将数据从一个地址空间复制到另外一个地址空间。典型的例子就是移动一个外部内存的区块到芯片内部更快的内存区。

System Management Mode (SMM)：在SMM下，当保存当前正在运行程序的整个上下文（Context）时，处理器切换到一个分离的地址空间。然后SMM指定的代码或许被透明的执行。当从SMM返回时，处理器将回到被系统管理中断之前的状态。

**Rowhammer attack**

bit翻转，因为写入时的电流会影响附近位置的内容

Trusted path

* **Trusted path in** How do you know you are talking to your OS and not to a program? password
* **Trusted path out** The system is about to show you something secret. How does it know another program is not intercepting it? Windows Vista

### 5.5 Hardware Security Modules

攻击方式

* Steal the keys while they’re in plaintext

* Side channel attacks

* Supply chain attacks

Hacking HSMs: Cut the Casing；Cold Boot（关机后内存上数据会保留一段时间容易泄漏）；Evil Maid（物理层面）

## Lecture 6 Operating Systems

### 6.1 TLS

![ssl tls handshake](image/ssl tls handshake.png)

SSL是TLS的前身，**HTTPS** (Secure HTTP) means you are running HTTP over TLS

TLS Downgrade Attacks：Old browsers/servers don’t support new cryptography.

### 6.2 Randomness

Pseudorandom vs. random

在密码学中，randomness用于key generation

**P**seudo**r**andom **N**umber **G**eneration 这个可以参考密码学那部分

Netscape PRNG存在的缺点：40bit keys，用时间做种子，可以获取进程信息

/dev/urandom：Entropy from running a computer enters the entropy pool. Process reads from that pool

**Basic RSA**

![rsa](image/rsa.png)

### 6.3 Unix Security

**Discretionary access control** 

Subjects

* Users

* Groups

* Processes make accesses on behalf of users belonging to particular groups

Objects

* Files, Directories

Operations

* Read, write, execute

**SUID/SGID**

SUID：If the SUID bit is set, the program is run with the permission of the program’s owner.执行文件的时候用owner的权限去执行

SGID：改文件属组的权限

### 6.4 Android Security

**Mandatory access control** 

Permissions explicitly granted by user to share data and abilities (access network, read files).

## Lecture 7 Side Channels

### 7.1 Attack Types

**Passive vs. Active Attacks**

Passive Attacks：攻击者只监听，观察信息

Active Attacks：change state，结合监听数据和更改数据

**Direct vs. Side Channel Attacks**

Direct Attacks：攻击者直接和受害者交互 eg. 钓鱼邮件；安装malware

Side Channel Attacks：攻击者会观察信息，并基于此做出推断。受害者意外地通过一些媒介泄露信息。eg. 获取用户typing；观察pin pad上的磨损模式

### 7.2 Timing Attacks

通过观察不同的执行时间长度，可以（概率地）推断出代码执行的路径。
