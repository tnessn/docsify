如果你不能或不想连接到外部网络，也可以选择搭建自己的私有网络。

设置前确保已经按照[PlatON安装指南](zh-cn/[Chinese-Simplified]-安装指南)安装好PlatON。

本文假设Ubuntu环境下工作目录为 `~/platon-node` ，Windows环境下工作目录为 `D:\platon-node`，请确保将 ***PlatON安装指南*** 中生成的`platon`程序已经拷贝到工作目录。注意后续均在工作目录下进行。

## 单节点环境

1.从[这里](https://download.platon.network/ethkey-windows-amd64.exe)下载对应平台的公私钥对生成工具`ethkey`到工作目录

















- Windows通过浏览器下载后修改文件名


```
D:\platon-node> move ethkey-windows-amd64.exe ethkey.exe


```

















- Ubuntu命令行


```
$ wget https://download.platon.network/ethkey-linux-amd64
$ mv ethkey-linux-amd64 ethkey


```

2.运行公私钥对生成工具`ethkey`生成节点ID和节点私钥

















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

3.生成coinbase账户（coinbase账户是矿工的钱包地址， 即当前出块的gas和增发奖励应该给到矿工指定的coinbase地址，对于创世区块，由于没有矿工，这个地址应该为0x0000000000000000000000000000000000000000），为方便测试，可以在创世区块为该账户预先分配一定的Energon

















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

4.生成创世块配置文件`platon.json`

从[这里](https://download.platon.network/platon.json)下载创世配置文件模板`platon.json`保存到工作目录下，修改 `your-node-pubkey` 为第2步生成的 ***节点ID*** ，使本地节点成为共识节点参与共识。修改 `your-account-address` 为第3步生成的 ***Address***。`platon.json`内容如下：


```
{
    "config": {
    "chainId": 300,
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
  "timestamp": "0x5c074288",
  "extraData": "0x00",
  "gasLimit": "0x3150000000",
  "difficulty": "0x40000",
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

5.生成节点私钥文件

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

6.根据创世配置文件初始化创世信息

















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

7.启动节点

















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

至此我们就搭建好了一个拥有单节点的 PlatON 私有网络。网络名称为 `platon`， 网络 `ID` 为`300`，你可以在你的私有 `PlatON` 网络中像在公网一样的单节点中执行任何操作。

8.后台不挂起运行
一般情况下，platon 进程一直在前台进行，这样我们就不能进行其他操作了，并且如果中途退出该终端，程序将退出。

Windows不支持后台运行。

Ubuntu下以nohup方式启动程序：


```
$ nohup ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey" &


```
当shell中提示nohup成功后再按下一次回车，确保不会因为误关闭终端引起的进程退出。

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

$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06


```
PublicKey是我们需要的 ***节点ID***， PrivateKey是对应的 ***节点私钥*** 。

3.若使用 `MPC` 功能还需增加如下步骤。若不需要或者已经配置过，则跳到第4步
















- 从[这里](https://download.platon.network/mpclib.tar.gz)下载 `MPC` 依赖lib，解压缩放在节点服务器上。这里我们放在节点路径下：~/platon-node/mpclib

















- 使用root用户或者sudo权限打开profile文件


```
$vi /etc/profile


```

















- 添加环境变量


```bash
export LD_LIBRARY_PATH=$PATH:/home/XXXX/platon-node/mpclib


```
其中，`XXXX` 在本机为 platon 用户。开发者需改为自己登录的用户。

4.修改创世块配置文件`platon.json`。

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

5.分别生成两个节点的nodekey文件，文件内容为节点私钥节点0的nodekey文件放在platon-node/data0/platon目录下，节点1的nodekey文件放在platon-node/data1/platon目录下。注意echo命令行参数为节点私钥，需要替换成第2步生成的 ***节点私钥***。

















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

6.为节点0初始化创世块信息，拉起节点0
















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

7.为节点1初始化创世块信息，拉起节点1
















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

8.当两个节点都提示共识成功，区块插入到链，则集群环境启动成功。

## 为节点启用MPC功能

如需要使用MPC计算功能，首先请确保编译出的是 `包含了MPC` 的执行程序，并且下载配置了依赖包。

**重要提示：**
**MPC计算功能仅能在集群环境下启用，并且当前只支持 Linux 环境。**

在启动节点时还需要如下操作：

1.增加如下参数选项以显式声明开启MPC计算功能


```





--mpc --mpc.ice ./cfg.server1.config --mpc.actor 0xa7e6d8a00ba33ea732b2c924e1edc4e4b753e9ca


```

选项说明：

| 选项 | 描述 |
| :------------ | :------------ |
| --mpc       | （必选）开启mpc计算功能 |
| --mpc.actor | （可选）mpc计算指定钱包地址，与合约参与指定地址保持一致（可通过rpc接口eth_setActor(Address)进行设置） |
| --mpc.ice   | （可选）mpc节点初始化ice配置（不指定则mpc计算默认启动8201端口）|

2.若启用 `--mpc.ice` 选项，则需预先在节点工作工作目录下配置 `cfg.server1.config` 文件。


```
MpcNode.Server.Endpoints=default -p 8201


```

3.-p 后面的8201端口可自行修改，但不可与系统其他端口重复。

4.查看日志./log/platon_mpc_xxx.log(xxx为当前节点进程号)如打印如下：


```
MPC ENGINE INIT SUCCESS


```
则mpc初始化成功。

