## COMP0025 Lecture Review

一些攻击模型

* **Passive Attacks**
  * **Ciphertext-Only Attacks (COA)**：攻击者只看得见ciphertexts
  * **Known-Plaintext Attacks (KPA)**：攻击者知道一些plaintexts和ciphertexts对

* **Active Attacks**
  * **Chosen-Plaintext Attacks (CPA)**：通过给Alice发送$m_j$来获取$c_j$
  * **Chosen-Ciphertext Attacks (CCA)**：通过给Bob发送$c_j$来获取$m_j$（好像也可以选择明文）任意制造或者选择一些密文，并得到其解密后的明文

[TOC]


### Lecture 1 Classical cryptography

#### 1.1 Historical Ciphers

* **Transposition ciphers（移项）**: letters are permuted according to some scheme

  * **Scytale**：木棍上缠绕（密钥是木棍粗细）

  * **Columnar Transposition**：根据密钥中字母顺序对plaintext分块进行排序

    ![Columnar Transposition - Example](image/Columnar Transposition - Example.png)

    **Breaking**：暴力破解，穷举每个可能长度的key的可能顺序，有$\sum_{k=1}^{l}{k!}$种可能性。<font color='red'>[problem]:</font>可能有多种解，如sepha->shape, heaps, phase

  * **Shift Ciphers**：Caesar cipher（字母向后位移三位），ROT13（字母位移13位）

    **Breaking**：穷举所有位移可能性（25种）

* **Substitution ciphers（替换）**: letters are substituted by other letters or symbols

  **Breaking**：穷举有$26!$种变换不能暴力破解，所以采用**字母频率**（**Letter Frequency**）统计破解，同时运用一些二元三元字母组合（**Bigram/Trigram Frequencies**）

  * **Polyalphabetic Substitution Ciphers**：多字元，eg. Vigenere

    ![Vignere](image/Vignere.png)

    **Breaking**：

    * 字元长度已知，可以分块对密文进行频率分析

    * 字元长度未知：**Kasiski’s Test**

      ![Breaking Polyalphabetic Substitution Ciphers](image/Breaking Polyalphabetic Substitution Ciphers.png)

  * **Playfair Cipher**

    * Same row: pick letters to the right, wrap around to the left if needed

    * Same column: pick letters to the bottom, wrap around to the top if needed

    * Span rectangle: pick letters on the opposite corners within the same row

    * Same letters or odd message length: inject/append a pre-defifined dummy letter (say x)

    ![Playfair Cipher Example](image/Playfair Cipher Example.png)

#### 1.2 The Mechanization of Encryption

* **Hebern Rotor Machine**：内部含有替换表，key用rotor初始化，像Vignere，频率分析COA破解
* **Enigma**：COA破解



### Lecture 2 Principles of modern cryptography and pseudorandomness

#### 2.1 Principles of modern cryptography

这一章主要是算法安全性理论不停地更新

**Private-Key Encryption Schemes**

![Private-Key Encryption Schemes](image/Private-Key Encryption Schemes.png)

![random select](image/random select.png)：表示以相同概率从集合里随机选择一个元素

 Π：一般用于表示对称加密的模式

* **One-Time Pad**

  <img src="image/One-Time Pad.png" alt="One-Time Pad" style="zoom:50%;" />

  $\{0,1\}^\lambda$：长度为$\lambda$的二进制字符串

* **Probability Distributions**

  ![Probability Distributions](image/Probability Distributions.png)

  K是随机的，但message M分布并不是随机的，因为明文是有意义的。

  <u>加密算法的目标就是要让密文C的分布变得uniform</u>

* **Perfect Secrecy**

  ![perfect secrecy](image/perfect secrecy.png)

  这个可以抵抗COA

* **Perfect Indistinguishability**

  ![Perfect Indistinguishability](image/Perfect Indistinguishability.png)

  ![Perfect Indistinguishability Priv](image/Perfect Indistinguishability Priv.png)

**<span style='color:red;'>Perfect Secrecy = Perfect Indistinguishability</span>**

*<u>证明OTP是Perfect Secrecy：</u>*

因为针对每一对m和c都只有唯一的一个k使加密算法成立（这个算法是bijective的）

![OTP perfect secrecy](image/OTP perfect secrecy.png)

**Limitations：**

* 每次加密都需要更换密钥，因为会泄漏明文（given *c*1 = *m*1 ⊕ *k* and *c*2 = *m*2 ⊕ *k*, then *c*1 ⊕ *c*2 = *m*1 ⊕ *m*2）。KPA可以破解不更换密钥k的OTP（given *m*1, *c*1, and *c*2, then *m*1 ⊕ *c*1 ⊕ *c*2 = *m*2）
* k和m一样长，加密大型文件很困难，存储k也需要很大空间

**<span style='color:blue;'>Goals</span>：**1⃣️re-used key2⃣️shorter key

⬇️

为什么OTP不能用shorter key？$\rightarrow$ 因为这样会使算法不是perfect secrecy：

![OTP cannot with shorter keys](image/OTP cannot with shorter keys.png)

**Perfect Secrecy的一般化——Shannon's Theorem**

⚠️注意这里有个前提条件：明文、密钥、密文的长度都相同

![shannon theorem](image/shannon theorem.png)

**<span style='color:blue;'>Goals</span>：可以设计一个算法有shorter密钥但没有perfect secrecy。但是，这个算法安全的前提是Advesary的算力有限制。因为这个算法可以被暴力破解。**

⬇️

**Information-theoretic vs Computational Security**

* Information-theoretic：没有信息泄漏，攻击者的算力无限，模型不实用
* Computational Security：会有信息泄漏，攻击者算力有限，模型实用

**Computational Security**

1⃣️限制Adversary的最大计算时间2⃣️设置Adversary能破解算法的概率

* Concrete Security：具体数值

  ![concrete security](image/concrete security.png)

  * Brute-Force Attack

  Con：计算机算力不好计算；Adversary的攻击算法也不好计算；微处理器的算力变化

* Asymptotic Security：用时间复杂度来描述计算时间和攻击成功概率

  背景知识

  1⃣️关于密钥长度的多项式polynomial

  * **Probabilistic Polynomial Time Algorithms**

    ![Probabilistic Polynomial Time Algorithms](image/Probabilistic Polynomial Time Algorithms.png)

    <u>Tips</u>:   (g ◦ f)(x) = g (f(x))；(f·g)(x) = f(x) · g(x)

  2⃣️关于密钥长度的极小值函数negligible

  * **Negligible Functions**

    ![negligible function](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/negligible function.png)
  
  **Negligible Success Probability**：A cryptographic scheme is *unbreakable in practice* if the probability of a successful attack by an arbitrary PPT adversary *A* is negligible.
  
  <u>Asymptotic Security定义：</u>
  
  ![asymptotic security](image/asymptotic security.png)
  
  Cons: 确定 *λ* 有点困难

**Computational Indistinguishability**

![computational indistinguishablity def](image/computational indistinguishablity def.png)

![indistinguishable encryption](image/indistinguishable encryption.png)

**Next** Can we build a *practical computationally secure* One-Time Pad?

#### 2.2 Pseudorandomness

**<span style='color:blue;'>Goals</span>** Can we build a *practical computationally secure* One-Time Pad?

**pseudorandom generator**

G把$\lambda$长度的密钥$k$变成$l(\lambda)$长度的串$G(k)$，然后用$G(k)$加密明文。对于每个$k$，生成的$G(k)$应该看起来都是随机的。

<img src="image/pseudorandom generator.png" alt="One-Time Pad" style="zoom:40%;" />

**Randomness Versus Pseudorandomness**

* True Randomness：从物理现象中生成；不能预测的；均匀分布
* Pseudorandomness：可以预测；计算上的不可分辨性（computational indistinguishablity），均匀分布

**Pseudorandom Generators (PRGs)**

![PRGs def](image/PRGs def.png)

两个关键：扩展 & 随机

**Brute-Force Attacks on PRGs**

PRGs输出的分布并不是均匀的，下图中， 输入长度为$\lambda$，输出长度为$2\lambda$，这种情况下产生的随机数只有$2^\lambda$种可能性，而正常的长度为$2\lambda$的随机数有$2^{2\lambda}$种可能性。

![PRG not uniform](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/PRG not uniform.png)

暴力破解：

【关于第二个为什么是$2^{-\lambda}$】G的输出结果只有$2^\lambda$种可能性，而正常的长度为$2\lambda$的随机数有$2^{2\lambda}$种可能性，所以D预测正确的可能性只有$2^\lambda \div 2^{2\lambda}=2^{-\lambda}$

![brute force PRGs](image/brute force PRGs.png)

**<span style='color:blue;'>Goals</span>** 如何用OWF来生成PRGs

**One-Way Functions**

![OWF](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/OWF.png)

已知x和f(x)，找不到一个x'使得f(x')=f(x)

单向函数 (*One*-*way* *function*)是一种具有下述特点的单射函数：对于每一个输入，函数值都容易计算（多项式时间），但是给出一个随机输入的函数值，算出原始输入却比较困难（无法在多项式时间内使用确定性图灵机计算）。

**One-Way Permutations**

![OWP](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/OWP.png)

injective： *f* ( *x* 1 ) = *f* ( *x* 2 )意味着*x* 1 = *x* 2

即使用了OWF仍然有可能泄漏变量：Example: Let *g* be a one-way function, then *f* (*x*1*,* *x*2) = (*x*1*,* *g*(*x*2)) is hard to invert but reveals *x*1. （g是OWF但是G泄漏了输入的一半）

**Hard-Core Predicates**

给定f(x)，对于任何多项式时间算法，不能以明显高于1/2的概率确定hc(x)，也就是猜测hc(x)能够有1/2的正确率。

![HCP](image/HCP.png)

**HC Predicate for Any One-Way Function**

给定任意一个OWF/OWP f，能构造一个不同的OWF/OWP g以及g的一个HCP。

![HCP for any OWF](image/HCP for any OWF.png)

这个hc计算的是x里任意几个比特的异或值（因为ri=0时就不计算xi，ri=1时就异或xi），这个定理实际含义：<u>如果f是任意OWF，那么f(x)隐藏了x的比特位的一个随机子集的异或值</u>。

**Pseudorandom Generators with Minimal Expansion** 只扩展一位

**<span style='color:blue;'>Goals</span>** 用上述hcp构造PSG

用OWP构造PSG（OWF也可以构造，但是太复杂了，不讨论）

![use hcp to construct PRG](image/use hcp to construct PRG.png)

为什么可以构造：s是随机选择的，f是OWP，所以f(s)也是伪随机的，hc(s)也是伪随机的（即使给定f(s)，因为s是随机的），所以总体是伪随机的。

**Pseudorandom Generators with Polynomial Expansion** 扩展多位

根据上一个迭代就能得到多位扩展

![hcp poly exp](image/hcp poly exp.png)

**<span style='color:blue;'>Goals</span>** Construction of a **computationally secure** One-Time Pad using a **Pseudorandom Generator**

**One-Time Pad with PRG**

![OTP with PRG](image/OTP with PRG.png)

indistinguishability against eavesdroppers

**<span style='color:blue;'>Goals</span>** 证明OTP with PRG的安全性

**Reduction Proofs**： 把问题转化为另一个更好解决的问题，⬇️证明流程

![process of resuction proof](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/process of resuction proof.png)

**Security Proof for OTP with PRG**

图中可以把D和A的模型等价，也就是把D reduction到A模型。证明是indistinguishability encryption

> 参考课本44-45的细节内容

![OTP with PRG security](image/OTP with PRG security.png)

OTP with PRG扩展到加密不定长的m：先用前面提到的expansion factor扩展G(k)，再取和m相等长度的位数加密。

**Stream Ciphers**：生成伪随机流的算法

**Security for Multiple Encryptions** ： 当窃听者A能够获取多个c时

![multi encryption](image/multi encryption.png)

这边构造的是一个反例：

![multi encryption security](/Users/zyt/Documents/zyt-uk/term1/comp25-cryptography/notes/image/multi encryption security.png)

**<span style='color:blue;'>Goals</span>** How can we construct practical computationally secure encryption schemes supporting “arbitrary” many encryptions per key?

****

#### 做题思路

* **Perfect secrecy/indistinguishibility**

  从定义上证明，有两种方法：

  1. 证明任意两个明文输出相同密文的概率是相同的（这种情况下一般是可以直接计算出两个概率的，直接对比数值）

     ![perfect secrecy](image/perfect secrecy.png)

     例子：

     ![OTP perfect secrecy](image/OTP perfect secrecy.png)

  2. 证明攻击者根据密文区分不了输入的是哪个明文

     ![Perfect Indistinguishability](image/Perfect Indistinguishability.png)

  从性质上证明，所有Perfect Secrecy都具有以下性质：

  ![OTP cannot with shorter keys](image/OTP cannot with shorter keys.png)

  例子：

  ![q2-1](image/q2-1.png)

  ![q2-2](image/q2-2.png)

*  **Pseudorandom Generators**

  1. 证明是PRG，一般都用反证法，其中可以用到PRG的的定义，针对G函数和随机选取的真随机，计算概率差值是否是negl：

     ![PRGs def](image/PRGs def.png)

     例子：

     ![q2-3](image/q2-3.png)

     ![q2-4](image/q2-4.png)

  2. 证明不是PRG的时候，可以用两种方法：构造反例&用定义证

     构造反例例子：

     ![q2-5](image/q2-5.png)

     ![q2-6](image/q2-6.png)

     用定义证例子：

     ![q2-7](image/q2-7.png)

     ![q2-8](image/q2-8.png)

  

### Lecture 3 Private-key Encryption

**<span style='color:blue;'>Goals</span>** How can we construct practical computationally secure encryption schemes supporting “arbitrary” many encryptions per key?

承接上一个Lecture，因为Lecture2构造了单次加密的computationally secure encryption schemes，这一个Lecture研究multi encryption的问题。

****

