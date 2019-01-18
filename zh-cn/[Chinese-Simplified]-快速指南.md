> 快速安装、搭建、测试PlatON网络

## 安装PlatON
PlatON支持不同的Linux、Windows、Docker等运行环境，各平台安装指南请参考[PlatON安装指南](zh-cn/[Chinese-Simplified]-安装指南)。

本文假设Ubuntu环境下工作目录为 ~/platon-node ，Windows环境下工作目录为 D:\platon-node。注意后续均在工作目录下进行。

## 连接到网络

### 连接到贝莱世界测试网络

贝莱世界测试网络现已开放。想要链接到测试网络，可以通过下列方式：

* 请下载 [Samurai 钱包](https://download.platon.network/Samurai-windows-amd64.exe)客户端，并移步[这里](zh-cn/[Chinese-Simplified]-Samurai-钱包)根据提示安装后连接。
* 使用交互式命令行工具 [了解更多](https://github.com/PlatONnetwork/wiki/wiki/_javascript-console)

### 搭建私有网络
如果限制于网络条件不能连接到外部网络，或者希望在本地测试验证PlatON，可[搭建私有网络](zh-cn/[Chinese-Simplified]-私有网络)。

### 连接到节点

可通过以下`http`方式 进入`platon`控制台
- Windows命令行


```
D:\platon-node> platon.exe attach http://localhost:6789


```

- Linux命令行：


```
$ ./platon attach http://localhost:6789


```

## 创建PlatON账户
为方便测试，我们可以在控制台方便地创建多个账户。


```
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0x3dea985c48e82ce4023263dbb380fc5ce9de95fd"


```

输出结果为生成的账户地址。

### 查看账户列表


```
> eth.accounts
["0x566c274db7ac6d38da2b075b4ae41f4a5c481d21", "0x3dea985c48e82ce4023263dbb380fc5ce9de95fd"]


```
本例中，`0x566c274db7ac6d38da2b075b4ae41f4a5c481d21`为[搭建私有网络](zh-cn/[Chinese-Simplified]-私有网络)时创建且已经在创世区块中预分配一定的Energon的coinbase账户。

## 解锁账户
发送交易前，需要解锁账户。


```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x566c274db7ac6d38da2b075b4ae41f4a5c481d21
Passphrase: 
true


```
输入创建账户时设置的密码，就可以成功解锁账户。

## 发送交易
通过控制台命令可在不同账户间发起转账交易，例如以下命令为从账户地址`0x566c274db7ac6d38da2b075b4ae41f4a5c481d21`转账到账户地址`0x3dea985c48e82ce4023263dbb380fc5ce9de95fd`。

可用实际生成的账户地址进行替换，注意必须加上`0x`前缀。

必须保证转出账户有足够的余额，如果连接到测试网络可到PlatON官网申请测试Energon，如果是自己搭建私有网络可在创世区块中预先分配Energon。


```
> eth.sendTransaction({from:"0x566c274db7ac6d38da2b075b4ae41f4a5c481d21",to:"0x3dea985c48e82ce4023263dbb380fc5ce9de95fd",value:10,gas:88888,gasPrice:3333})
"0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b"


```

返回结果为交易Hash，以下命令可查询交易详情。


```
> eth.getTransaction("0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b")
{
  blockHash: "0xf5d3da50065ce5793c9571a031ad6fe5f1af326a3c4fb7ce16458f4d909c1613",
  blockNumber: 33,
  from: "0x566c274db7ac6d38da2b075b4ae41f4a5c481d21",
  gas: 88888,
  gasPrice: 3333,
  hash: "0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b",
  input: "0x",
  nonce: 0,
  r: "0x433fe5845391b6da3d8aa0d2b53674e09fb6126f0070a600686809b57e4ef77d",
  s: "0x6b0086fb76c46024f849141074a5bc79c49d5f9a658fd0fedbbe354889c34d8d",
  to: "0x3dea985c48e82ce4023263dbb380fc5ce9de95fd",
  transactionIndex: 0,
  v: "0x1b",
  value: 10
}


```

### 查看账户余额


```
> eth.getBalance("0x566c274db7ac6d38da2b075b4ae41f4a5c481d21")
998999999999999999990


```