---
title: 以太坊初体验 —— 建立一条私有链
date: 2018-11-01 04:11:37
description: 本文将带大家了解并尝试建立一条属于自己的私有链。
categories: 教程
tags: [环境配置, 教程, Go, hello-world]
toc: true
---

# 以太坊初体验 —— 建立一条私有链

本文将讲述如何在电脑上，本地搭建一个以太坊私有链。

本文将解决下面问题：

- [x] 以太坊的安装
- [x] 私有链创世区块搭建
- [x] 了解区块各字段的意思
- [x] 了解日志输出
- [x] 学习使用remix
- [x] 编写简单的智能合约
- [x] 部署智能合约到私有链上
- [x] 了解交易各字段的意思
- [ ] 私有链节点的加入

## 配置环境

1. Windows 10 （1803）
2. go version go1.11.1 darwin/amd64
3. Geth Version: 1.8.17-stable
4. 文件操作目录 `~/helloEth`

## 搭建私有链

> 从 〇 到 壹 。

### 01/ 安装Geth

#### Windows 用户

1. 下载 Geth 安装包 （[官网下载地址](https://ethereum.github.io/go-ethereum/downloads/)）
2. 安装 Geth。 
3. 完成！

#### Mac 用户

0. 安装「Homebrew」
    ```shell
    $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

1. 升级「Homebrew」
   ```shell
   brew update
   ```

2. 安装 Geth
   ```shell
   brew tap ethereum/ethereum
   brew install ethereum
   ```

3. 完成！

#### 检验安装是否成功

```shell
$ geth version
Geth
Version: 1.8.17-stable
```

### 02/ 配置创世区块参数

1. 创建操作目录文件夹。该文件夹将用于存储以太坊的账号以及相关数据。
   ```shell
   $ mkdir ~/helloEth
   $ cd ~/helloEth
   ```

2. 编写创世区块配置文件 `genesis.json`
   ```shell
   $ vim genesis.json
   ```
   在 genesis.json中输入下述代码
   ```json
   {
    	"config": {
        	"chainId": 613,
        	"homesteadBlock": 0,
        	"eip155Block": 0,
        	"eip158Block": 0
    	},
    	"alloc": {},
    	"difficulty": "20"
    	"gasLimit": "80860000"
   }
   ```
   
   **参数讲解**

   | 相关参数 | 含义 |
   | --------- | --------- |
   | chainId | 指定了独立的区块链网络ID。网络ID在连接到其他节点的时候会用到，以太坊公网的网络ID是 1，为了不与公有链网络冲突，运行私有链节点的时候要指定自己的网络ID。不同ID网络的节点无法相互连接。 |
   | homesteadBlock | 以太坊的早期版本需要的配置值，当设置为0表示使用Homestead发布该链。 |
   | eip15xBlock | 处理硬分叉等事宜，私有链无关 |
   | difficulty | 设置设置当前区块的难度，值越大挖矿就越难。 |
   | gasLimit | 该值设置对GAS的消耗总量限制，用来限制区块能包含的交易信息总和，即智能合约的消耗上限 |
   | alloc | 用来预置账号以及账号的以太币数量。 |

### 03/ 初始化创世区块

```shell
$ geth --datadir "./" init genesis.json
```
执行完上述命令可见以下输出反馈：
```shell
INFO [11-03|15:37:49.070] Maximum peer count                       ETH=25 LES=0 total=25
INFO [11-03|15:37:49.248] Allocated cache and file handles         database=D:\\geth\\helloEth\\geth\\chaindata cache=16 handles=16
INFO [11-03|15:37:49.341] Writing custom genesis block
INFO [11-03|15:37:49.341] Persisted trie from memory database      nodes=0 size=0.00B time=0s gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-03|15:37:49.342] Successfully wrote genesis state         database=chaindata                           hash=570bab…526737
INFO [11-03|15:37:49.342] Allocated cache and file handles         database=D:\\geth\\helloEth\\geth\\lightchaindata cache=16 handles=16
INFO [11-03|15:37:49.475] Writing custom genesis block
INFO [11-03|15:37:49.475] Persisted trie from memory database      nodes=0 size=0.00B time=0s gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [11-03|15:37:49.476] Successfully wrote genesis state         database=lightchaindata                           hash=570bab…526737
```
> 上述 **INFO** 信息分别表示：当前允许的最大 peer 数量，分配的缓存和文件处理者，写入自定义的创世区块，从内存数据库中读取持久化的 trie，成功写入了创世区块。

与此同时会在当前目录创建geth和keystore两个文件夹

- **geth目录**：保存链上的区块数据。
- **keystore目录**：保存链上的用户信息。

### 04/ 启动区块链

执行以下命令：

```shell
$ geth --port 8086 --networkid 613 --nodiscover --datadir="./" --maxpeers=0 --rpc --rpcport 9527 --rpcaddr 127.0.0.1 --rpccorsdomain "*" --rpcapi "eth,net,web3,personal,miner"
```

**参数讲解**

| 相关参数 | 具体参数 | 含义 |
| --------- | --------- | --------- |
| port | 8086 | 网络监听端口，以太坊服务所运行在的网络端口号 |
| networkid | 613 | 在以太坊网络的唯一标识，可设置为任意值，但是不能与以太坊的保留值冲突，建议范围 [100 ~ 10000] |
| nodiscover | - | 使用这个参数，你的节点就不会被其他人发现，除非手动添加你的节点。 |
| datadir | "./" | 私有链存放路径 |
| maxpeers | 0 | 最大网络用户数，设为 0 时无人能够连上该的测试链。 |
| rpc | - | 启动 HTTP-RPC 服务器。这个参数在Geth上是默认的。 |
| rpcport | 9527 | RPC 服务器端口 |
| rpcaddr | 127.0.0.1 | RPC 服务器地址 |
| rpccorsdomain | "*" | RPC 服务器域名 |
| rpcapi | "eth,net,web3,personal,miner" | 这个命令指示了允许通过RPC访问的命令。默认情况下，Geth允许web3。 |

执行完上述命令可见以下输出反馈：

```shell
INFO [11-03|15:44:47.832] Maximum peer count                       ETH=0 LES=0 total=0
INFO [11-03|15:44:47.887] Starting peer-to-peer node               instance=Geth/v1.8.17-stable-8bbe7207/windows-amd64/go1.11.1
INFO [11-03|15:44:47.887] Allocated cache and file handles         database=D:\\geth\\helloEth\\geth\\chaindata cache=768 handles=1024
INFO [11-03|15:44:48.076] Initialised chain configuration          config="{ChainID: 613 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: <nil> EIP155: 0 EIP158: 0 Byzantium: <nil> Constantinople: <nil> Engine: unknown}"
INFO [11-03|15:44:48.077] Disk storage enabled for ethash caches   dir=D:\\geth\\helloEth\\geth\\ethash count=3
INFO [11-03|15:44:48.077] Disk storage enabled for ethash DAGs     dir=C:\\Users\\inpluslab_PC\\AppData\\Ethash count=2
INFO [11-03|15:44:48.077] Initialising Ethereum protocol           versions="[63 62]" network=613
INFO [11-03|15:44:48.077] Loaded most recent local header          number=0 hash=570bab…526737 td=20 age=49y6mo2w
INFO [11-03|15:44:48.077] Loaded most recent local full block      number=0 hash=570bab…526737 td=20 age=49y6mo2w
INFO [11-03|15:44:48.077] Loaded most recent local fast block      number=0 hash=570bab…526737 td=20 age=49y6mo2w
INFO [11-03|15:44:48.080] Regenerated local transaction journal    transactions=0 accounts=0
INFO [11-03|15:44:48.081] Starting P2P networking
INFO [11-03|15:44:48.084] IPC endpoint opened                      url=\\\\.\\pipe\\geth.ipc
INFO [11-03|15:44:48.085] HTTP endpoint opened                     url=http://127.0.0.1:9527 cors=* vhosts=localhost
INFO [11-03|15:44:48.159] RLPx listener up                         self="enode://d469dd263b54e2f7d96e99f8fd01c88c731647f993b561f20d7d51f25636a5eac4e08b85b639e0047f82918b177bee3e3508bf553d59a521752faff2d94f875a@222.200.179.26:8086?discport=0"
INFO [11-03|15:44:49.088] Mapped network port                      proto=tcp extport=8086 intport=8086 interface=NAT-PMP(192.168.199.1)
```

> 上述 **INFO** 信息分别表示：当前最大 peer 数量，启动 P2P 节点，分配缓存和文件管理者，初始化链的配置，允许硬盘存储上的缓存和 DAG，初始化以太坊协议，加在最近的头部、全区块、快速区块，重新生成本地交易记录，开启 P2P 网络，RLPx 监听启动，IPC 和 HTTP 启动。

***截至到现在，我们已经成功完成搭建一条私有链的目标。完成了从零到一的过程。下面我们将学习区块链的操作，完成从1到10000的目标。***

## 操作私有链

> 从 1 到 10000000 。

### 01/ 连接 Geth Javascript 客户端

区块链成功运行起来后，我们需要添加账户、开始挖矿、转账、添加智能合约等等一系列的操作。而这些操作，我们将通过 Geth Javascript 客户端进行交互与操作。

下面，我们打开一个新的命令窗口，运行下述命令创建客户端。

```shell
$ geth attach http://127.0.0.1:9527
```

> 通过该命令，我们连接到了我们刚刚创建好的RPC-HTTP服务器上，其端口号与地址为我们刚刚设定的值。

运行成功后，我们可以看到：

```shell
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.17-stable-8bbe7207/windows-amd64/go1.11.1
modules: eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 web3:1.0

> （此处一个闪烁的光彪）
```

### 02/ 创建账号
在命令行中输入下述命令：

```shell
> personal.newAccount('littleBear')
```
上述命令通过给newAccount传递密码作为参数，完成了账号的创建。

创建成功后，会返回一个账号地址：
```shell
"0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1"
```

> 通常，第一个创建的账号为默认的`coinbase`账号

### 03/ 成为矿工

**开始挖矿**
```shell
> miner.start()
```

等待一定的时间，我们便可以在服务器的状态栏，输出log中，看到类似于下述的输出，表明矿工挖到了矿：

```shell
INFO [11-03|16:01:15.212] Etherbase automatically configured       address=0x86f36e6d3D7d0A417B8C9f92f6269fc2210A05f1
INFO [11-03|16:01:52.358] Updated mining threads                   threads=4
INFO [11-03|16:01:52.358] Transaction pool price threshold updated price=1000000000
INFO [11-03|16:01:52.359] Commit new mining work                   number=1 sealhash=016c7e…328e2a uncles=0 txs=0 gas=0 fees=0 elapsed=0s
INFO [11-03|16:01:53.526] Successfully sealed new block            number=1 sealhash=016c7e…328e2a hash=881be3…a7d7c3 elapsed=1.166s
INFO [11-03|16:01:53.526] 🔨 mined potential block                  number=1 hash=881be3…a7d7c3
INFO [11-03|16:01:53.528] Commit new mining work                   number=2 sealhash=3ed01d…2939e1 uncles=0 txs=0 gas=0 fees=0 elapsed=1.998ms
INFO [11-03|16:01:53.878] Successfully sealed new block            number=2 sealhash=3ed01d…2939e1 hash=a37654…801018 elapsed=350.799ms
INFO [11-03|16:01:53.878] 🔨 mined potential block                  number=2 hash=a37654…801018
INFO [11-03|16:01:53.878] Commit new mining work                   number=3 sealhash=06d074…42d329 uncles=0 txs=0 gas=0 fees=0 elapsed=0s
INFO [11-03|16:01:54.110] Successfully sealed new block            number=3 sealhash=06d074…42d329 hash=ab6d14…91e71f elapsed=231.866ms
INFO [11-03|16:01:54.110] 🔨 mined potential block                  number=3 hash=ab6d14…91e71f
INFO [11-03|16:01:54.110] Commit new mining work                   number=4 sealhash=85c7a5…c98ca0 uncles=0 txs=0 gas=0 fees=0 elapsed=0s
INFO [11-03|16:01:55.241] Successfully sealed new block            number=4 sealhash=85c7a5…c98ca0 hash=3ebc5a…d08aa3 elapsed=1.130s
INFO [11-03|16:01:55.241] 🔨 mined potential block                  number=4 hash=3ebc5a…d08aa3
INFO [11-03|16:01:55.241] Commit new mining work                   number=5 sealhash=59080e…649102 uncles=0 txs=0 gas=0 fees=0 elapsed=0s
INFO [11-03|16:01:55.398] Successfully sealed new block            number=5 sealhash=59080e…649102 hash=41a3fe…c7ce91 elapsed=156.910ms
INFO [11-03|16:01:55.398] 🔨 mined potential block                  number=5 hash=41a3fe…c7ce91
INFO [11-03|16:01:55.398] Mining too far in the future             wait=2s
```

**停止挖矿**
```shell
> miner.stop()
```

### 04/ 查看余额

成为矿工，辛辛苦苦挖矿之后，我们来查看下自己的余额。

有以下两种方式查询：

```shell
>  web3.fromWei(eth.getBalance(eth.coinbase), "ether")

115
```
> 该查询方式的单位为常见的单位，一以太。

```shell
> eth.getBalance("0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1")

115000000000000000000
```
> 该查询方式以 Wei 作为计价单位，所以看上去会很多，实际上换算成以太币需要去掉 18 个 0。

与此同时，我们来看看矿工挖到的区块，由什么组成：
```shell
> eth.getBlock("latest")
```
得到：
```json
{
  difficulty: 137676,
  extraData: "0xda83010811846765746888676f312e31312e318777696e646f7773",
  gasLimit: 56439934,
  gasUsed: 0,
  hash: "0x5c5fcb11f83dfc48d897746479f02a0d91b6f34151c1a343a7cc9cef392ad7e5",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1",
  mixHash: "0x1e7a0a50ccaf49a8425af24bd97cc1b4969faf6ebfc837ec64adac29be07d158",
  nonce: "0x30c08cd4f24a9590",
  number: 368,
  parentHash: "0x79cf067b0d7f8b2d519942afa93c036480bca34067e7315e3179ca28cda54dcf",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 541,
  stateRoot: "0xa07db2428d21dcd44ebe67b3ca3fcc084b197295e7ebffbdd2b45e7aa1ea3463",
  timestamp: 1541236677,
  totalDifficulty: 50366901,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}
```
**参数讲解**

| 相关参数 | 含义 |
| --------- | --------- |
| difficulty | 当前区块的设定难度 |
| extraData | 区块的额外数据 | 
| gasLimit | 区块最多允许使用的 gas 量 | 
| gasUsed | 区块中交易使用掉的 gas 量 |
| hash | 区块的哈希值 | 
| logsBloom | 当前区块的 bloom filter | 
| miner | 挖出该区块的矿工账号 |
| mixHash | 由 nonce 值得出，参与验证 PoW | 
| nonce | 用于验证 PoW 的随机值 | 
| number | 区块编号 | 
| parentHash | 上一个区块的哈希值 | 
| receiptsRoot | 当前区块的回执的哈希根值 | 
| sha3Uncles |  区块的 uncle 数据的 SHA3 |
| size | 区块大小 |
| stateRoot | 区块的最终状态的哈希根值 |
| timestamp | 时间戳 |
| totalDifficulty | 整个链耗费在当前区块的时间 |
| transactions | 区块所包含的所有交易 | 
| transactionsRoot | 所有交易的哈希根值 |
| uncles | uncle 哈希的数组 | 

### 05/ 转账交易

我们通过挖矿挖到了币，下面我们来尝试转账交易。

1. 创建新用户。转账至少需要两个人，所以我们创建多一个账户。
    ```shell
    > personal.newAccount("littlePig")

    "0x791e1755a91b042a39ecb30b6b8b480779ea5200"
    ```
2. 查看双方余额。
    ```shell
    > eth.getBalance("0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1")

    885000000000000000000
    > eth.getBalance("0x791e1755a91b042a39ecb30b6b8b480779ea5200")

    0
    ```
3. 解锁账户。资金的转移，需要先进行账户解锁。
    ```shell
    > personal.unlockAccount("0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1")

    Unlock account 0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1
    !! Unsupported terminal, password will be echoed.
    Passphrase: littleBear

    true
    ```
4. 转账。
    ```shell
    > eth.sendTransaction({from:"0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1", to:"0x791e1755a91b042a39ecb30b6b8b480779ea5200", value: 520})

    "0x22521335039a7e73536579afc6b6ab8c87c4ef0994397a2ed20b63445964effe"
    ```
5. 挖矿。转账完成后，如果立刻通过eth.getBalance()查看余额，会发现并没有变化。这是因为sendTransaction这是发起了一笔交易事务，还没有得到确认，只是将这个事务放到了待提交池中。区块链的机制中是新创建区块的时候，会就从事务池中找出所有事务，进行有效性验证，验证成功后进行挖矿并将所有相关事务打包到区块中，待新的去区块成功加入到区块链中后，之前的转账就得到了最终的确认和永久固话。所以，我们需要挖矿。
    ```shell
    > miner.start()
    ```
6. 查看双方余额。转账成功。
    ```shell
    > eth.getBalance("0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1")

    914999999999999999480
    > eth.getBalance("0x791e1755a91b042a39ecb30b6b8b480779ea5200")

    520
    ```

完成了交易，我们来看看交易具体发生了什么，看看 transaction：
```shell
> eth.getTransaction("0x22521335039a7e73536579afc6b6ab8c87c4ef0994397a2ed20b63445964effe")
```
得到：
```shell
{
  blockHash: "0x140ebfcbdf6395222a5011e1f494f0edcbfa618552ce50c2d61d116b522c606f",
  blockNumber: 178,
  from: "0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1",
  gas: 90000,
  gasPrice: 1000000000,
  hash: "0x22521335039a7e73536579afc6b6ab8c87c4ef0994397a2ed20b63445964effe",
  input: "0x",
  nonce: 0,
  r: "0xe52112a3e8de4e34aeb79639f916a46b7b5aa0f5a9de3a31bc8fb3dd73eaa6b0",
  s: "0x6dcd3492f292a181d7ea562bd5d02ec4c3a4c0090e357dd06658978636a689b9",
  to: "0x791e1755a91b042a39ecb30b6b8b480779ea5200",
  transactionIndex: 0,
  v: "0x4ee",
  value: 520
}
```

***截至到现在，我们已经成功完成学习区块链的操作，完成从1到10000的目标。下面，我们将开始学习部署智能合约并调用。***

## 部署并调用智能合约

> 从 黑白 到 彩色 。

### 01/ 编写智能合约

在以太坊中，智能合约主要由 **Solidity** 语言编写而成。我们将通过该语言编写出智能合约，并部署到我们建立的区块链中，执行并调用。

以下为 **Solidity** 语言编写的 *Hello World* 

```java
contract Mortal {
    /* Define variable owner of the type address */
    address owner;

    /* This function is executed at initialization and sets the owner of the contract */
    function Mortal() { owner = msg.sender; }

    /* Function to recover the funds on the contract */
    function kill() { if (msg.sender == owner) selfdestruct(owner); }
}

contract HelloWorld is Mortal {
    /* Define variable greeting of the type string */
    string helloworld = "Hello World!";

    /* This runs when the contract is executed */
    function SayHi(string _helloworld) public {
        helloworld = _helloworld;
    }

    /* Main function */
    function sayHi() constant returns (string) {
        return helloworld;
    }
}
```
### 02/ 编译智能合约

在编写完智能合约后，我们需要将其编译，使其成为字节码的形式来供我们部署到区块链中。

随着区块链的发展，我们现在不必在本地部署繁琐的开发环境，可直接通过在线的编译器，将我们编写的智能合约编译成为字节码。

借助 [Remix 在线编译器](https://remix.ethereum.org/) ，我们将刚刚编写的智能合约编译成为可部署的命令。

编译后得到如下：
```javascript
var helloworldContract = web3.eth.contract([{"constant":true,"inputs":[],"name":"sayHi","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[],"name":"kill","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"name":"_helloworld","type":"string"}],"name":"SayHi","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"}]);
var helloworld = helloworldContract.new(
   {
     from: web3.eth.accounts[0], 
     data: '0x606060405260408051908101604052600c81527f48656c6c6f20576f726c642100000000000000000000000000000000000000006020820152600190805161004b92916020019061006c565b5060008054600160a060020a03191633600160a060020a0316179055610107565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106100ad57805160ff19168380011785556100da565b828001600101855582156100da579182015b828111156100da5782518255916020019190600101906100bf565b506100e69291506100ea565b5090565b61010491905b808211156100e657600081556001016100f0565b90565b610322806101166000396000f3006060604052600436106100565763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416630c49c36c811461005b57806341c0e1b5146100e55780639d44799a146100fa575b600080fd5b341561006657600080fd5b61006e61014b565b60405160208082528190810183818151815260200191508051906020019080838360005b838110156100aa578082015183820152602001610092565b50505050905090810190601f1680156100d75780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b34156100f057600080fd5b6100f86101f4565b005b341561010557600080fd5b6100f860046024813581810190830135806020601f8201819004810201604051908101604052818152929190602084018383808284375094965061023595505050505050565b61015361024c565b60018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156101e95780601f106101be576101008083540402835291602001916101e9565b820191906000526020600020905b8154815290600101906020018083116101cc57829003601f168201915b505050505090505b90565b6000543373ffffffffffffffffffffffffffffffffffffffff908116911614156102335760005473ffffffffffffffffffffffffffffffffffffffff16ff5b565b600181805161024892916020019061025e565b5050565b60206040519081016040526000815290565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061029f57805160ff19168380011785556102cc565b828001600101855582156102cc579182015b828111156102cc5782518255916020019190600101906102b1565b506102d89291506102dc565b5090565b6101f191905b808211156102d857600081556001016102e25600a165627a7a7230582029daeb769f67cf5f6efb52aaf0fc06d6bbb4f66aac5b73c2731a4e1e70fd81300029', 
     gas: '4700000'
   }, function (e, contract){
    console.log(e, contract);
    if (typeof contract.address !== 'undefined') {
         console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
    }
 })
```

### 03/ 部署智能合约

将我们刚刚编译好的命令，复制，粘贴进我们的命令行中，回车。

完成部署！

```shell
> null [object Object]
Contract mined! address: 0xa97586f92acd398ecdc0a7ee328b4bccb5f24f71 transactionHash: 0x02320730498ecb2c97177d3559968cfa486eadb0849d47d2e3d8d385f5399007
```

尝试调用 helloworld ：

```shell
> helloworld.sayHi()

"Hello World!"
```

智能合约实际上是部署在交易上面的，那么我们来看看含有智能合约的交易字段变成何样子：

```shell
>  eth.getTransaction("0x02320730498ecb2c97177d3559968cfa486eadb0849d47d2e3d8d385f5399007")
```
得到：
```json
{
  blockHash: "0x401c7add802323234e57c436735bf2b3c4e0efd22b1142c7a726f3b663cadac5",
  blockNumber: 251,
  from: "0x86f36e6d3d7d0a417b8c9f92f6269fc2210a05f1",
  gas: 4700000,
  gasPrice: 1000000000,
  hash: "0x02320730498ecb2c97177d3559968cfa486eadb0849d47d2e3d8d385f5399007",
  input: "0x606060405260408051908101604052600c81527f48656c6c6f20576f726c642100000000000000000000000000000000000000006020820152600190805161004b92916020019061006c565b5060008054600160a060020a03191633600160a060020a0316179055610107565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106100ad57805160ff19168380011785556100da565b828001600101855582156100da579182015b828111156100da5782518255916020019190600101906100bf565b506100e69291506100ea565b5090565b61010491905b808211156100e657600081556001016100f0565b90565b610322806101166000396000f3006060604052600436106100565763ffffffff7c01000000000000000000000000000000000000000000000000000000006000350416630c49c36c811461005b57806341c0e1b5146100e55780639d44799a146100fa575b600080fd5b341561006657600080fd5b61006e61014b565b60405160208082528190810183818151815260200191508051906020019080838360005b838110156100aa578082015183820152602001610092565b50505050905090810190601f1680156100d75780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b34156100f057600080fd5b6100f86101f4565b005b341561010557600080fd5b6100f860046024813581810190830135806020601f8201819004810201604051908101604052818152929190602084018383808284375094965061023595505050505050565b61015361024c565b60018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156101e95780601f106101be576101008083540402835291602001916101e9565b820191906000526020600020905b8154815290600101906020018083116101cc57829003601f168201915b505050505090505b90565b6000543373ffffffffffffffffffffffffffffffffffffffff908116911614156102335760005473ffffffffffffffffffffffffffffffffffffffff16ff5b565b600181805161024892916020019061025e565b5050565b60206040519081016040526000815290565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061029f57805160ff19168380011785556102cc565b828001600101855582156102cc579182015b828111156102cc5782518255916020019190600101906102b1565b506102d89291506102dc565b5090565b6101f191905b808211156102d857600081556001016102e25600a165627a7a7230582029daeb769f67cf5f6efb52aaf0fc06d6bbb4f66aac5b73c2731a4e1e70fd81300029",
  nonce: 1,
  r: "0x42a5c8e6cc0ab969a5169796471e37a1d53a0af04f1401f3a40af4996a046a25",
  s: "0x15fa0475fefe40e8d510dc80dbf3c92cfed3e4a4ec2771616427fad9857ae8f5",
  to: null,
  transactionIndex: 0,
  v: "0x4ee",
  value: 0
}
```

变化主要接种在value中，value为 0 代表该交易用以部署智能合约。

## 尾声

至此，我们已经完成了创建一条私有链，操作私有链，部署智能合约并了解其中相关名词的含义的步骤。

**主要参考资料**

1. [Mac环境搭建以太坊私有链](https://segmentfault.com/a/1190000015350715)
2. [怎样用Remix编译和使用智能合约](https://www.jianshu.com/p/50e81eb88088)
3. [以太坊私有链环境下的智能合约的编写、编译、创建与执行](https://blog.csdn.net/m0_37327416/article/details/72885521)