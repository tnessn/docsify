如果你不能或不想连接到外部网络，也可以选择搭建自己的私有网络。

设置前确保已经按照[PlatON安装指南](zh-cn/[Chinese-Simplified]-安装指南)安装好PlatON。

本文假设Ubuntu环境下工作目录为 `~/platon-node` ，Windows环境下工作目录为 `D:\platon-node`。注意后续均在工作目录下进行。

## 单节点环境

1.运行公私钥对生成工具`ethkey`生成节点ID和节点私钥











- Windows命令行：


```
D:\platon-node> ethkey.exe genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey :  8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127


```











- Ubuntu命令行：


```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127


```
PublicKey是我们需要的 ***节点ID***， PrivateKey是对应的 ***节点私钥*** 。

2.生成coinbase账户（coinbase账户是矿工的钱包地址， 即当前出块的gas和增发奖励应该给到矿工指定的coinbase地址，对于创世区块，由于没有矿工，这个地址应该为0x0000000000000000000000000000000000000000），为方便测试，可以在创世区块为该账户预先分配一定的Energon











- Windows命令行：


```
D:\platon-node> platon.exe --datadir .\data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}


```











- Ubuntu命令行：


```
$ ./platon --datadir ./data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}


```
记下生成的**Address**

3.生成创世块配置文件`platon.json`

从[这里](https://download.platon.network/platon.json)下载创世配置文件模板`platon.json`保存到工作目录下，修改 `your-node-pubkey` 为第2步生成的 ***节点ID*** ，使本地节点成为共识节点参与共识。修改 `your-account-address` 为第3步生成的 ***Address***。`platon.json`内容如下：


```
{
    "config": {
    "chainId": 100,
    "homesteadBlock": 1,
    "eip150Block": 2,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 3,
    "eip158Block": 3,
    "byzantiumBlock": 4,
    "cbft": {
      "initialNodes": ["enode://your-node-pubkey@127.0.0.1:16789"]
    }
  },
  "nonce": "0x0",
  "timestamp": "0x5bc94a8a",
  "extraData": "0x00000000000000000000000000000000000000000000000000000000000000007a9ff113afc63a33d11de571a679f914983a085d1e08972dcb449a02319c1661b931b1962bce02dfc6583885512702952b57bba0e307d4ad66668c5fc48a45dfeed85a7e41f0bdee047063066eae02910000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x99947b760",
  "difficulty": "0x1",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "your-account-address": {
      "balance": "999000000000000000000"
    }
  },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}


```

4.生成节点私钥文件

注意echo命令行参数为节点私钥，需要替换成第2步生成的 ***节点私钥***。











- Windows命令行：


```
D:\platon-node> mkdir .\data\platon
D:\platon-node> echo 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36> .\data\platon\nodekey
D:\platon-node> type .\data\platon\nodekey


```











- Ubuntu命令行：


```
$ mkdir -p ./data/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data/platon/nodekey
$ cat ./data/platon/nodekey


```

5.根据创世配置文件初始化创世信息











- Windows命令行：


```
D:\platon-node> platon.exe --datadir .\data init platon.json


```











- Ubuntu命令行：


```
$ ./platon --datadir ./data init platon.json


```
出现以下提示说明初始化创世信息完成。


```
Successfully wrote genesis state


```

6.启动节点











- Windows命令行：


```
D:\platon-node> platon.exe --identity "platon" --datadir .\data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data\platon\nodekey"


```











- Ubuntu命令行：


```
$ ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey"


```

***提示：***

| 选项 | 描述 |
| :------------ | :------------ |
| --identity | 指定网络名称 |
| --datadir  | 指定data目录路径 |
| --rpcaddr  | 指定rpc服务器地址 |
| --rpcport  | 指定rpc协议通信端口 |
| --rpcapi   | 指定节点开放的rpcapi名称 |
| --rpc      | 指定http-rpc通讯方式 |
| --nodiscover | 不开启节点发现功能 |
| --nodekey | 指定节点私钥文件保存路径 |

此时在标准输出中出现 blockNumber 增长的日志记录，**表示共识成功，链成功启动，并成功出块**。

至此我们就搭建好了一个拥有单节点的 PlatON 私有网络。网络名称为 `platon`， 网络 `ID` 为`100`，你可以在你的私有 `PlatON` 网络中像在公网一样的单节点中执行任何操作。

7.后台不挂起运行
一般情况下，platon 进程一直在前台进行，这样我们就不能进行其他操作了，并且如果中途退出该终端，程序将退出。

Ubuntu下以nohup方式启动程序：


```
$ nohup ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey" &


```
当shell中提示nohup成功后再按下一次回车，确保不会因为误关闭终端引起的进程退出。

**Windows不支持后台运行。**

## PlatON 集群环境
`PlatON集群`是有多节点参与的网络环境，这里我们假设你已经可以构建一个PlatON单节点。并且，我们将构建的是两个节点组成的网络。更多的节点在操作流程上类似。

为了在本地运行platon多节点，你要确保：










- 每个节点实例拥有单独的data目录（--datadir）










- 每个实例运行在不同的端口上，不管是platon还是rpc（--port and --rpcport ）










- 节点必须知道对方的存在










- ipc端口要么禁止，要么唯一


1.在platon-node目录下创建目录data0和data1，作为两个节点的数据目录。分别生成两个节点的coinbase账户。










- Windows命令行：


```
D:\platon-node> mkdir data0
D:\platon-node> platon.exe --datadir .\data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

D:\platon-node> mkdir data1
D:\platon-node> platon.exe --datadir .\data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}


```











- Ubuntu命令行：


```
$ mkdir -p data0
$ ./platon --datadir ./data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

$ mkdir -p data1
$ ./platon --datadir ./data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}


```

2.使用ethkey工具分别为两个节点生成节点ID和节点私钥










- Windows命令行：


```
D:\platon-node> ethkey.exe genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey :  8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

D:\platon-node> ethkey.exe genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06


```











- Ubuntu命令行：


```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

$ ./ethkey genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06


```
PublicKey是我们需要的 ***节点ID***， PrivateKey是对应的 ***节点私钥*** 。

3.修改创世块配置文件`platon.json`。

将两个节点的节点信息加入 **initialNodes** 数组中，因为我们生成的是两个节点组成的集群环境，所以数组长度为2。
需要修改`platon.json`文件：










- `node0-pubkey`为步骤2生成的节点0的 ***节点ID*** 










- `node1-pubkey`为步骤2生成的节点1的 ***节点ID*** 










- `node0-account-address`为步骤1生成的节点0的 ***Address***










- `node1-account-address`为步骤1生成的节点1的 ***Address***


```
……
  "cbft": {
  "initialNodes": [
    "enode://node0-pubkey@127.0.0.1:16789",
    "enode://node1-pubkey@127.0.0.1:16790"]
  }
……
  "alloc": {
    "node0-account-address": {
      "balance": "999000000000000000000"
    },
    "node1-account-address": {
      "balance": "999000000000000000000"
    }
  },
……


```

4.分别生成两个节点的nodekey文件，文件内容为节点私钥节点0的nodekey文件放在platon-node/data0/platon目录下，节点1的nodekey文件放在platon-node/data1/platon目录下。注意echo命令行参数为节点私钥，需要替换成第2步生成的 ***节点私钥***。











- Windows命令行：


```
D:\platon-node> mkdir .\data0\platon
D:\platon-node> echo 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36> .\data0\platon\nodekey
D:\platon-node> type .\data0\platon\nodekey

D:\platon-node> mkdir .\data1\platon
D:\platon-node> echo 9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320> .\data1\platon\nodekey
D:\platon-node> type .\data1\platon\nodekey


```











- Ubuntu命令行：


```
$ mkdir -p ./data0/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data0/platon/nodekey
$ cat ./data0/platon/nodekey

$ mkdir -p ./data1/platon
$ echo "9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320" > ./data1/platon/nodekey
$ cat ./data1/platon/nodekey


```

5.为节点0初始化创世块信息，拉起节点0










- Windows命令行：


```
D:\platon-node> platon.exe --datadir .\data0 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data0\platon\nodekey"


```











- Ubuntu命令行：


```
$ ./platon --datadir ./data0 init platon.json
$ ./platon --identity "platon" --datadir ./data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data0/platon/nodekey"


```

6.为节点1初始化创世块信息，拉起节点1










- Windows命令行：


```
D:\platon-node> platon.exe --datadir .\data1 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data1\platon\nodekey"


```
在Windows下除第一个节点外，其他节点都需要使用--ipcdisable启动。











- Ubuntu命令行：


```
$ ./platon --datadir ./data1 init platon.json
$ ./platon --identity "platon" --datadir ./data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data1/platon/nodekey"


```

7.当两个节点都提示共识成功，区块插入到链，则集群环境启动成功。

8.不挂起运行

像单节点一样，为使程序在Linux平台上后台不挂起运行，可按如下方式来启动节点：


```bash
$ nohup ./platon ... --nodekey "./data0/platon/nodekey" >> node0.log 2>&1 &

$ nohup ./platon ... --nodekey "./data1/platon/nodekey" >> node1.log 2>&1 &


```
每次 nohup 执行后同样需按下回车键。

## 为节点启用MPC功能

MPC 计算功能是PlatON 平台支持的安全多方计算功能，为实现隐私计算提供基础设施。更多MPC相关请[参考这里](zh-cn/[Chinese-Simplified]-%e9%9a%90%e7%a7%81%e5%90%88%e7%ba%a6%e5%bc%80%e5%8f%91%e6%8c%87%e5%8d%97)。**MPC计算功能仅能在[集群环境](#PlatON-%e9%9b%86%e7%be%a4%e7%8e%af%e5%a2%83)下启用，并且当前只支持 Ubuntu 系统。**

如需要使用MPC计算功能，首先务必确保是用[源码编译安装](zh-cn/[Chinese-Simplified]-%e5%ae%89%e8%a3%85%e6%8c%87%e5%8d%97#%e6%ba%90%e7%a0%81%e7%bc%96%e8%af%91%e5%ae%89%e8%a3%85)带 `MPC` 功能的 `platon` 二进制可执行程序。并且下载配置了 mpclib 依赖库文件。

在启动节点时还需要如下操作：

1.检查依赖库


```bash
$ env | grep LD_LIBRARY_PATH
LD_LIBRARY_PATH=/home/platon/platon-node/mpclib


```

2.MPC计算服务通信使用单独的端口，默认为8201。若在一台机器上启用多个计算节点，需启用 `--mpc.ice` 选项，以显式声明的方式开启MPC计算功能


```










--mpc --mpc.ice ./{mpc-ice-config-file} --mpc.actor 0xa7e6d8a00ba33ea732b2c924e1edc4e4b753e9ca


```

选项说明：

| 选项 | 描述 |
| :------------ | :------------ |
| --mpc       | （必选）开启mpc计算功能 |
| --mpc.ice   | （可选）mpc节点初始化ice配置。配置文件为 ./{mpc-ice-config-file} |
| --mpc.actor | （可选）mpc计算指定钱包地址，与隐私合约参与方地址保持一致（可通过rpc接口eth_setActor(Address)进行设置） |


3.配置文件设置。若节点启动时启用 `--mpc.ice` 选项，则需要预先在节点工作目录下配置 `{mpc-ice-config-file}` 文件，用以配置 `MPC通信端口`，保持默认端口即可。文件配置如下


```
MpcNode.Server.Endpoints=default -p 8201


```

**注意**：

**`-p` 后面的端口默认8201，可自行修改但不可与系统其他端口重复。**

4.这里因为两个节点部署在同一台机器上，所以指定两个端口用以区分。


```
$ touch cfg.server0.config
$ echo "MpcNode.Server.Endpoints=default -p 10001" > cfg.server0.config

$ touch cfg.server1.config
$ echo "MpcNode.Server.Endpoints=default -p 10002" > cfg.server1.config


```

5.启动节点


```
$ nohup ./platon ... --nodekey "./data0/platon/nodekey" --mpc --mpc.ice ./cfg.server0.config --mpc.actor 0x9a568e649c3a9d43b7f565ff2c835a24934ba447 >> node0.log 2>&1 &
$ nohup ./platon ... --nodekey "./data1/platon/nodekey" --mpc --mpc.ice ./cfg.server1.config --mpc.actor 0xce3a4aa58432065c4c5fae85106aee4aef77a115 >> node1.log 2>&1 &


```

6.查看日志./log/platon_mpc_xxx.log(xxx为当前节点进程号)如打印如下：


```
MPC ENGINE INIT SUCCESS


```
则mpc初始化成功。

