## COMP0025 Lecture Review

一些攻击模型

* **Passive Attacks**
  * **Ciphertext-Only Attacks (COA)**：攻击者只看得见ciphertexts
  * **Known-Plaintext Attacks (KPA)**：攻击者知道一些plaintexts和ciphertexts对

* **Active Attacks**
  * **Chosen-Plaintext Attacks (CPA)**：通过给Alice发送$m_j$来获取$c_j$
  * **Chosen-Ciphertext Attacks (CCA)**：通过给Bob发送$c_j$来获取$m_j$（好像也可以选择明文）

[TOC]


### Lecture 1 Classical cryptography

#### 1.1 Historical Ciphers

* **Transposition ciphers（移项）**: letters are permuted according to some scheme

  * **Scytale**：木棍上缠绕（密钥是木棍粗细）

  * **Columnar Transposition**：根据密钥中字母顺序对plaintext分块进行排序

    ![Columnar Transposition - Example](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Columnar Transposition - Example.png)

    **Breaking**：暴力破解，穷举每个可能长度的key的可能顺序，有$\sum_{k=1}^{l}{k!}$种可能性

  * **Shift Ciphers**：Caesar cipher（字母向后位移三位），ROT13（字母位移13位）

    **Breaking**：穷举所有位移可能性（25种）

* **Substitution ciphers（替换）**: letters are substituted by other letters or symbols

  **Breaking**：穷举有$26!$种变换不能暴力破解，所以采用**字母频率**（**Letter Frequency**）统计破解，同时运用一些二元三元字母组合（**Bigram/Trigram Frequencies**）

  * **Polyalphabetic Substitution Ciphers**：多字元，eg. Vigenere

    ![Vignere](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Vignere.png)

    **Breaking**：

    * 字元长度已知，可以分块对密文进行频率分析

    * 字元长度未知：**Kasiski’s Test**

      ![Breaking Polyalphabetic Substitution Ciphers](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Breaking Polyalphabetic Substitution Ciphers.png)

  * **Playfair Cipher**

    * Same row: pick letters to the right, wrap around to the left if needed

    * Same column: pick letters to the bottom, wrap around to the top if needed

    * Span rectangle: pick letters on the opposite corners within the same row

    * Same letters or odd message length: inject/append a pre-defifined dummy letter (say x)

    ![Playfair Cipher Example](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Playfair Cipher Example.png)

#### 1.2 The Mechanization of Encryption

* **Hebern Rotor Machine**：内部含有替换表，key用rotor初始化，像Vignere，频率分析COA破解
* **Enigma**：COA破解



### Lecture 2 Principles of modern cryptography and pseudorandomness

#### 2.1 Principles of modern cryptography

这一章主要是算法安全性理论不停地更新，用图表示会更清晰：



**Private-Key Encryption Schemes**

![Private-Key Encryption Schemes](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Private-Key Encryption Schemes.png)

* **One-Time Pad**

  <img src="/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/One-Time Pad.png" alt="One-Time Pad" style="zoom:50%;" />

* **Probability Distributions**

  ![Probability Distributions](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Probability Distributions.png)

  加密算法的目标就是要让C的分布变得uniform

* **Perfect Secrecy**

  ![perfect secrecy](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/perfect secrecy.png)

* **Perfect Indistinguishability**

  ![Perfect Indistinguishability](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Perfect Indistinguishability.png)

  ![Perfect Indistinguishability Priv](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Perfect Indistinguishability Priv.png)

**<span style='color:red;'>Perfect Secrecy = Perfect Indistinguishability</span>**

*<u>证明OTP是Perfect Secrecy：</u>*

因为针对每一对m和c都只有唯一的一个k使加密算法成立（这个算法是bijective的）

![OTP perfect secrecy](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/OTP perfect secrecy.png)

**Limitations：**

* 每次加密都需要更换密钥，因为会泄漏明文。KPA可以破解不更换密钥k的OTP
* k和m一样长，加密大型文件很困难，存储k也需要很大空间

**<span style='color:blue;'>Goals</span>：**1⃣️re-used key2⃣️shorter key

⬇️

为什么OTP不能用shorter key？$\rightarrow$ 因为这样会使算法不是perfect secrecy：

![OTP cannot with shorter keys](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/OTP cannot with shorter keys.png)

**Perfect Secrecy的一般化——Shannon's Theorem**

⚠️注意这里有个前提条件：明文、密钥、密文的长度都相同

![shannon theorem](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/shannon theorem.png)

**<span style='color:blue;'>Goals</span>：可以设计一个算法有shorter密钥但没有perfect secrecy。但是，这个算法安全的前提是Advesary的算力有限制。因为这个算法可以被暴力破解。**

⬇️

**Information-theoretic vs Computational Security**

* Information-theoretic：没有信息泄漏，攻击者的算力无限，模型不实用
* Computational Security：会有信息泄漏，攻击者算力有限，模型实用

**Computational Security**

1⃣️限制Adversary的最大计算时间2⃣️设置Adversary能破解算法的概率

* Concrete Security：具体数值

  * Brute-Force Attack

  Con：计算机算力不好计算；Adversary的攻击算法也不好计算；微处理器的算力变化

* Asymptotic Security：

  背景知识

  1⃣️关于密钥长度的多项式polynomial

  * **Probabilistic Polynomial Time Algorithms**

    ![Probabilistic Polynomial Time Algorithms](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Probabilistic Polynomial Time Algorithms.png)

    <u>Tips</u>:   (g ◦ f)(x) = g (f(x))；(f·g)(x) = f(x) · g(x)

  2⃣️关于密钥长度的极小值函数negligible

  * **Negligible Functions**

    ![Negligible Functions](/Users/zyt/Documents/zyt-uk/comp25-cryptography/notes/image/Negligible Functions.png)
