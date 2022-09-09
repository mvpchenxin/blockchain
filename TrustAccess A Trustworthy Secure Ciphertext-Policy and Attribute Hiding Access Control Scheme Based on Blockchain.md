`2022-09-02——2022-09-11`

### TrustAccess: A Trustworthy Secure Ciphertext-Policy and Attribute Hiding Access Control Scheme Based on Blockchain

区块链技术背景下，针对基于属性的访问控制，提出一种新的基于区块链的可信安全密文策略和属性隐藏访问控制方案。

本文思路，第一，将CP-ABE与区块链结合；第二，将属性隐私化处理。

第一点改进`Fully Secure Cipertext-Policy Hiding CP-ABE`论文。

接下来研究两点：`CP-ABE、CP-ABE与区块链结合`

**CP-ABE**

ABE是一种公钥加密算法。加密算法核心就是将（属性、策略）嵌入到（密钥K、密文C）中。CP-ABE（基于密文策略的属性加密）是将策略嵌入到密文中，属性嵌入到密钥中。数据拥有者可以通过设定策略决定拥有哪些属性的人能够访问这份密文，也就相当于对这份数据做了一个细粒度可以细化到属性级别的加密访问控制。

###### Fully Secure Cipertext-Policy Hiding CP-ABE

**系统架构**（区块链+CP-ABE）

包含三部分：数据所有者、数据用户、联盟区块链

![image-20220906114110750](C:\Users\mvpchenxin\AppData\Roaming\Typora\typora-user-images\image-20220906114110750.png)

步骤：

1、DO负责制定访问策略，并使用HP-CP-ABE加密数据

2、DO通过存储交易将密文地址发送给CB

3、DU在本地将属性集合与隐藏策略进行匹配，并通过EIGamal同态加密进行加密

4、匹配成功，DU提交访问证明

5、智能合约验证DU提交的访问证明

6、DO生成访问交易到CB进行共识验证

7、DU从密钥处获取密钥解密由地址索引的密文

**HP-CP-ABE**

Setup($1^{\lambda}$) $\rightarrow$ $(PK，MSK)$

Enc$(PK，m，T)$ $\rightarrow$ $CT$

KeyGen$(PK，MSK)$ $\rightarrow$ $SK_{\mathop{v}\limits ^{\rightarrow}}$

Dec$(PK，CT，S，SK_{\mathop{v}\limits ^{\rightarrow}})$ $\rightarrow$ $m$ or $\bot$ 

**方案目标**

1、保证DO拥有的数据机密性，实现细粒度的可信的数据访问控制

2、访问控制策略完全被隐藏（密文策略），只有符合条件的用户才能访问数据，而不会泄露任何个人信息（基于属性加密）。

3、能够满足实际访问控制需求（有效性和可扩展性）。

**方案总体进程**

![image-20220906170701308](C:\Users\mvpchenxin\AppData\Roaming\Typora\typora-user-images\image-20220906170701308.png)

1、设置阶段：

DO与DU分别初始化系统

加密阶段：

指定一个访问策略

加密数据并隐藏这个策略

生成密文

2、链上阶段：

DO生成一个存储交易传递给CB，交易包含上述隐藏访问策略的密文地址

CB将该交易广播给DU，DU在本地匹配属性与策略，如果匹配成功，DU生成证明

DU将该证明广播给链上其他节点，触发部署在节点上的智能合约，验证DU提交证明的有效性

CB将验证结果广播给DO

DO生成一个密钥，传递到CB，形成共识，传递到DU

3、访问阶段：

访问交易达成共识，DU获得密钥，解密由地址索引的密文（注意密钥本地存储在DO，不能被CB访问，将自动分发，无需人工干预），数据访问成功。

进程过程遇到两个问题：一是DU属性如何匹配密文策略。二是解密完得到策略明文以后，需不需要再次匹配才可以访问数据（或者说DU解密完以后得到了什么，是明文策略还是DO数据）。