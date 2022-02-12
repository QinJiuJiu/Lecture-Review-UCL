## COMP0054 Lecture Review

[TOC]

### Lecture 1 

Design security system $\rightarrow$ Define security system (Meet security policy) $\rightarrow$ Define security policy (Create threat model)

#### 1.1 Introduction and Ethics

Code of conduct 

#### 1.2 What is Security?

Safety vs. Security

* Safety：抵抗accident，抵御nature，可根据历史数据建模
* Security：抵抗intentional攻击，抵抗intelligent beings，要对攻击者的策略进行建模

Implications of Digital Information：复制容易；传播快；存储方便；来源难查

**CIA Triangle**

![CIA Triangle](/Users/zyt/Documents/zyt-uk/comp54-computer-security-1/notes/image/CIA Triangle.png)

eg. Mass surveillance(C&I);spam(I&A);data breach(C因为没有修改data)

**Binary Model & Risk Management Model**

* Binary Model：secure & insecure
* Risk Management Model：没有完全的安全和不安全

The Security Mindset：ant的例子（可以给任何人邮寄ants）

#### 1.3 Threat Modelling

* Threats （攻击者是谁）$\rightarrow$ Attacker-centric

* Vulnerabilities （系统的漏洞在哪）$\rightarrow$ System-centric

* Likelihood attack（发生的可能性）

* Impact （attack发生后的影响）$\rightarrow$ Asset-centric

* Protection （保护措施的cost）

A system is “secure” if an adversary constrained by a **specific threat model** cannot violate the **security policy**.

**Security mechanism**: Technical mechanism used to ensure that the security policy is not violated by an adversary operating within the threat model.

#### 1.4 Threat Modelling: Some Examples

* threats（capabilities&motivation）
* vulnerabilities（capabilities&vulnerabilities）
* likelihood（motivation&capabilities&vulnerabilities分析发生可能性）
* impact（motivation&scale）
* protection（vulnerabilities&cost）

#### 1.5 Design Principles [SS’73]

* Least privilege 分析用户需要多少权限 eg.linux文件权限分配

* Separation of responsibilities 分层设计，不同人员管理不同内容

* Complete mediation 多次检查权限的更新，保持功能性

* Fail-safe default eg.accessing outside internet白名单而不是黑名单

* Defence in depth 安全保护的程度

* Open design eg.让密钥保密而不是加密算法保密

* Psychological acceptability 设计尽量实用

* Economy of mechanisms 简单

**Trusted computing base (TCB)** refers to every component of the system upon which the security policy relies (could be hardware, software, etc.)

Security + security ≠ security（eg.每解决一个bug可能产生更多新的bug，安全策略的融合可能并不安全）

#### 1.6 Design Principles: Computer Architecture

Code is data：代码和数据一起存储，计算性能提升但是容易被恶意利用（eg.bof）

**Abstraction:** specifying meaning and behaviour of software while hiding implementation details 

**Modular code** exploits abstraction and enables composition and reuse

**Layers**

* Pros：更好的并存；可以忽略被其他层解决的问题

* Cons：上层难以解决底层问题；漏洞可能传播；单层缺陷会影响上层

**Moore’s Law**：circuit density增加；computer performance roughly doubles every 18-24 months

**Halting Problem**：anything sufficiently interesting cannot be determined about a program $\rightarrow$ Security can never be solved!



### Lecture 2

#### 1.1 Access Control: Introduction

