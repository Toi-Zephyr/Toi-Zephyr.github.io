---
title: Bitcoin 相关名词
date: 2018-09-09 17:53:33
description: 比特币中的相关专有名词
categories: Bitcoin
tags: [Bitcoin]
toc: true
---

# Bitcoin 专有名词详解

## address

比特币地址看起来像 1DSrfJdB2AnWaFNgSbv3MZC2m74996JafV。它由一串字母和数字组成。它实际上是一个公钥160位哈希编码的 base58check 版本。就像你要求别人发送电子邮件到你的电子邮件地址, 你会要求别人把你的比特币发送到你的比特币地址之一。

> A bitcoin address looks like 1DSrfJdB2AnWaFNgSbv3MZC2m74996JafV. It consists of a string of letters and numbers. It’s really an encoded base58check version of a public key 160-bit hash. Just like you ask others to send an email to your email address, you would ask others to send you bitcoin to one of your bitcoin addresses. 

## bip ( Bitcoin Improvement Proposals )

比特币改进建议。一组由比特币社区成员提交来改进比特币的提案。例如, BIP-21 是一个改进比特币统一资源标识符 (URI) 方案的建议。

> Bitcoin Improvement Proposals. A set of proposals that members of the bitcoin community have submitted to improve bitcoin. For example, BIP-21 is a proposal to improve the bitcoin uniform resource identifier (URI) scheme. 

## bitcoin 

货币单位 (硬币)、网络和软件的名称。

> The name of the currency unit (the coin), the network, and the software. 

## block 

以时间戳标记的事务分组和上一个块的指纹。块标头被散列以生成工作证明, 从而验证事务。通过网络协商一致, 将有效块添加到主块链中。

> A grouping of transactions, marked with a timestamp, and a fingerprint of the previous block. The block header is hashed to produce a proof of work, thereby validating the transactions. Valid blocks are added to the main blockchain by network consensus. 

## blockchain 

经过验证的块的列表, 每一个都链接到它的前身到创世纪块。

> A list of validated blocks, each linking to its predecessor all the way to the genesis block. 

## Byzantine Generals Problem 

一个可靠的计算机系统必须能够应付一个或多个组件的故障。失败的组件可能呈现一种经常被忽略的行为类型, 即, 将冲突信息发送到系统的不同部分。解决这类失败的问题被抽象地表达为拜占庭将军的问题。

> A reliable computer system must be able to cope with the failure of one or more of its components. A failed component may exhibit a type of behavior that is often overlooked—namely, sending conflicting information to different parts of the system. The problem of coping with this type of failure is expressed abstractly as the Byzantine Generals Problem. 

## coinbase

> A special field used as the sole input for coinbase transactions. The coinbase allows claiming the block reward and provides up to 100 bytes for arbitrary data. Not to be confused with Coinbase transaction. 

## coinbase transaction

> The first transaction in a block. Always created by a miner, it includes a single coinbase. Not to be confused with Coinbase. 

## cold storage 

是指保持比特币的储备离线。当比特币私钥创建并存储在安全的脱机环境中时, 就会实现冷存储。冷库对于任何拥有比特币的人来说都很重要。在线计算机易受黑客攻击, 不应用于存储大量的比特币。

> Refers to keeping a reserve of bitcoin offline. Cold storage is achieved when Bitcoin private keys are created and stored in a secure offline environment. Cold storage is important for anyone with bitcoin holdings. Online computers are vulnerable to hackers and should not be used to store a significant amount of bitcoin. 

## colored coins 

一种开源的比特币2.0协议，使开发人员能够利用其超出货币的功能在比特币区块链上创建数字资产。

> An open source Bitcoin 2.0 protocol that enables developers to create digital assets on top of bitcoin blockchain utilizing its functionalities beyond currency. 

## confirmations 

一旦交易包含在一个区块中，它就有一个确认。 只要在同一区块链上挖掘另一个区块，该交易就会有两个确认，依此类推。 六次或更多次确认被认为足以证明交易无法逆转。

> Once a transaction is included in a block, it has one confirmation. As soon as another block is mined on the same blockchain, the transaction has two confirmations, and so on. Six or more confirmations is considered sufficient proof that a transaction cannot be reversed. 

## consensus 

当几个节点（通常是网络上的大多数节点）在其本地验证的最佳块链中都具有相同的块。 不要与共识规则混淆。

> When several nodes, usually most nodes on the network, all have the same blocks in their locally-validated best block chain. Not to be confused with consensus rules. 

## consensus rules

完整节点遵循的块验证规则与其他节点保持一致。 不要与共识混淆。

> The block validation rules that full nodes follow to stay in consensus with other nodes. Not to be confused with consensus. 

## difficulty 

一种网络范围的设置，用于控制生成工作证明所需的计算量。

> A network-wide setting that controls how much computation is required to produce a proof of work. 

## difficulty retargeting

网络范围内对每2016块发生一次的困难进行重新计算, 并考虑前2016个块的哈希功率。

> A network-wide recalculation of the difficulty that occurs once every 2,016 blocks and considers the hashing power of the previous 2,016 blocks.

## difficulty target 

网络中的所有计算大约每10分钟发现一次块的难度。

> A difficulty at which all the computation in the network will find blocks approximately every 10 minutes. 

## double spending 

双重支出是成功多花一些钱的结果。 比特币通过验证添加到区块链中的每个交易来防止双重支出，以确保以前没有花费过交易的输入。

> Double spending is the result of successfully spending some money more than once. Bitcoin protects against double spending by verifying each transaction added to the block chain to ensure that the inputs for the transaction had not previously already been spent. 

## ECDSA 

椭圆曲线数字签名算法或ECDSA是比特币使用的加密算法，以确保资金只能由其合法所有者使用。

> Elliptic Curve Digital Signature Algorithm or ECDSA is a cryptographic algorithm used by Bitcoin to ensure that funds can only be spent by their rightful owners. 

## extra nonce 

随着难度的增加, 矿工们经常在没有找到一个街区的情况下循环通过所有40亿的数值。因为 coinbase 脚本可以存储在2到100字节的数据中, 所以矿工们开始使用该空间作为额外的空间, 这样他们就可以探索更大范围的块标头值来找到有效的块。

> As difficulty increased, miners often cycled through all 4 billion values of the nonce without finding a block. Because the coinbase script can store between 2 and 100 bytes of data, miners started using that space as extra nonce space, allowing them to explore a much larger range of block header values to find valid blocks.
