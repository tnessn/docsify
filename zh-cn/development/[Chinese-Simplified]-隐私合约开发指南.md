- [简介](#简介)
- [架构](#架构)
- [快速开始](#快速开始)
   - [搭建构建环境](#搭建构建环境)
   - [隐私合约实现](#隐私合约实现)
   - [隐私合约编译](#隐私合约编译)
   - [隐私合约发布](#隐私合约发布)
   - [隐私数据服务实现](#隐私数据服务实现)
   - [隐私客户端实现](#隐私客户端实现)
- [隐私合约](#隐私合约)
  - [隐私计算对象](#隐私计算对象)
  - [隐私计算函数](#隐私计算函数)
  - [使用范例](#使用范例)
- [隐私数据服务SDK](#隐私数据服务SDK)
  - [MPC回调接口](#MPC回调接口)
- [隐私客户端SDK](#隐私客户端SDK)
  - [发起计算](#发起计算)
  - [获取任务ID](#获取任务ID)
  - [获取结果密文](#获取结果密文)
  - [获取交易回执](#获取交易回执)
  - [获取结果明文](#获取结果明文)
  - [其他](#其他)

## 简介

[PlatON](zh-cn/basics/[Chinese-Simplified]-快速指南) 平台作为下一代`Trustless`安全数据计算架构，为实现隐私计算提供了基础设施，安全多方计算(`MPC`) 为隐私计算提供安全执行的协议实现。PlatON公链网络和外部计算网络组合成一个数据为中心的复合网络，为用户提供安全计算服务，实现数据的价值的流动。在 `PlatON`平台上，**MPC计算虚拟机**(`MPC VM`) 作为 PlatON 计算架构中关键组件，其使用**隐私合约**进行`MPC`计算，并提供了稳定的运行时服务。

## 架构

<div align=left>
<img src="zh-cn/development/privacy-contract/images/mpc_structure.png" width = "650" height="523"/>  
</div>
架构图说明：

- PlatON

  在这里是带有 MPC 功能的 `PlatON` 区块链节点。

- Privacy Contract

   隐私合约，一种特殊的安全多方计算(`MPC`) 合约，使用 `C++` 语言进行开发，运行在 PlatON 节点的 `MPC`执行虚拟机(`MPC VM`) 里。隐私合约借助 PlatON 节点提供的 MPC 能力，实现了对计算方数据的隐私保护。

- MPC VM

   `MPC`执行虚拟机，提供隐私合约运行时环境，并在底层执行 MPC 计算对象，实现 MPC 算法，其依托于 PlatON 节点提供的计算服务。

- Privacy App

  隐私应用客户端，作为隐私计算发起方，向节点发起计算请求，查询计算结果等。需要基于[隐私客户端SDK](#隐私客户端SDK)进行开发。

- Privacy Data Service

  隐私数据服务，作为隐私计算数据提供方，向计算节点提供数据。该服务与`MPC VM`关联起来，参与MPC计算。需要基于[隐私数据服务SDK](#隐私数据服务SDK)进行开发。

- Data Source

  数据源，为隐私数据服务提供原始数据流（如数据库，文件，网络流等），是隐私数据的来源。

## 快速开始 

现在使用MPC领域的经典案例 [姚氏百万富翁问题](https://en.wikipedia.org/wiki/Yao%27s_Millionaires%27_Problem) 开始隐私合约开发介绍。

**案例表述**：

两个百万富翁`Alice`和`Bob`想知道他们两个谁更富有，但他们都不想让对方知道自己财富的任何信息。

**解决方案**：

使用`MPC`计算，两个富翁可以在不知道对方具体财产的情况下，与对方进行比较，并计算出谁最富有。

### 搭建构建环境

下面以Ubuntu16.04操作系统为例搭建构建环境，我们假设 ${privacy-contract-compiler} 目录为 Ubuntu 对应目录 `/home/platon/privacy-contract-compiler`，  {pWasm} 为 Ubuntu 对应目录 `/home/platon/pWasm` ，其中 `platon` 为当前登录用户。用户对应自己登录的用户即可。

**依赖组件：**

- PlatON 集群环境

隐私合约运行依赖于`PlatON` 网络集群环境，提供区块链的核心功能，想要让节点提供 MPC 计算能力必须启用MPC计算功能。

   1. 安装带 `MPC` 功能的 `platon` 可执行程序，查看[安装指南](zh-cn/%5BChinese-Simplified%5D-%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)；

   2. 集群环境搭建，查看 [PlatON集群环境](zh-cn/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#PlatON+%e9%9b%86%e7%be%a4%e7%8e%af%e5%a2%83)；

   3. 在节点启用 MPC 计算功能，查看[启用MPC计算功能](zh-cn/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#%e4%b8%ba%e8%8a%82%e7%82%b9%e5%90%af%e7%94%a8MPC%e5%8a%9f%e8%83%bd)。

- protobuf 3.5.2

在隐私合约中用 [protobuf](https://github.com/protocolbuffers/protobuf) 来实现自定义结构体，并提供pb消息打包和解包能力。当前 protobuf 需为 `3.5.2` 版本，安装步骤如下：

```bash
# 下载v3.5.2代码
$ git clone https://github.com/protocolbuffers/protobuf.git --recursive
$ cd protobuf
$ git checkout v3.5.2
# 编译安装
$ mkdir -p build
$ cd build && cmake ..
$ sudo make && make install
```

- plang 编译器

`plang` 是`PlatON`定制的编译器，用来编译隐私合约生成 `Wasm智能合约` 源代码文件、JAVA代理程序。官网提供了 [privacy-contract-compiler](https://github.com/PlatONnetwork/privacy-contract-compiler#Build)  开源工具包，下载后解压到 ${privacy-contract-compiler} 目录，按照 **README** 安装即可。

**注意：**
plang 编译器集成了 `llvm` 和 `clang 编译器` 源码包，运行时依赖于 clang ，它需要与 clang 安装到同一个目录。  

- emp-tool 库

使用 [emp-tool](https://github.com/PlatONnetwork/emp-tool) 提供核心的MPC计算对象，下载合约包使用以下命令。

```bash
$ git clone https://github.com/PlatONnetwork/emp-tool.git
```

emp-tool/include目录包含所需头文件：

```bash
include/
├── batcher.h
├── bit.h
├── block.h
├── comparable.h
├── constants.h
├── emp-tool.def
├── float_circuit.h
├── integer.h
├── number.h
└── swappable.h
```

### 隐私合约实现

编写一个隐私合约 `YaoMillionairesProblem.cpp` 解决姚氏百万富翁问题。代码实现如下：

```c++
#include <iostream>
#include "integer.h" // 使用emp-tool库头文件

/*
 * Yao's millionaires' problem, show who is richer.
 * @param money1, Alice参与方
 * @param money2, Bob参与方
 * @return true if Alice >= Bob else false.
 */
bool YaoMillionairesProblem(int money1, int money2) {
    std::cout << __FUNCTION__ 
        << " Alice: " << money1 
        << " Bob: " << money2 
        << std::endl;
	
    emp::Integer alice_money(money1, emp::ALICE);  // 设置Alice金额
    emp::Integer bob_money(money2, emp::BOB);  // 设置Bob金额

    int ret = (alice_money - bob_money).reveal();  // 执行比较大小，获取比较结果
    std::cout << __FUNCTION__ << " result(=Alice-Bob): " << ret << std::endl;

    return ret >= 0;
}
```

当前，隐私合约项目需要在 `plang` 项目 `make` 之后进入 `{privacy-contract-compiler}/build/bin` 目录下创建工程目录，这里为 `YaoProblem`。

```bash
$ pwd
/home/platon/privacy-contract/privacy-contract-compiler/build/bin
$ mkdir YaoProblem && cd YaoProblem
```

保存以上代码为文件`YaoMillionairesProblem.cpp`，将代码文件保存到 `YaoProblem` 目录下

### 隐私合约编译

编译隐私合约前，拷贝 `emp-tool/include` 到工程目录 `YaoProblem`，查看工作区目录树类似如下：

```bash
.
├── clang                                     # clang 编译器
├── plang                                     # plang编译器
├── YaoProblem                                # 合约开发目录
|   ├── config.json                           # 配置文件
|   ├── YaoMillionairesProblem.cpp            # 隐私合约
|   └── include                               # emp-tool库头文件
|       ├── integer.h                         # 隐私计算对象头文件
|       └── ...
└── ...
```

`config.json` 为配置两个数据提供方基本信息的配置文件。用户需改为自己提供数据服务的账户地址。配置如下：

```json
{
    "invitor": "0x9a568e649c3a9d43b7f565ff2c835a24934ba447",
    "parties": [
        "0x9a568e649c3a9d43b7f565ff2c835a24934ba447",
        "0xce3a4aa58432065c4c5fae85106aee4aef77a115"
    ],
    "method-price": {
        "YaoMillionairesProblem": 200000
    },
    "profit-rules": {
        "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219": 1,
        "0x3771c08952f96e70af27324de11bb380ec388ec3": 2
    },
    "urls": {
        "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219": "DirectNodeServer:default -h 127.0.0.1 -p 10001",
        "0x3771c08952f96e70af27324de11bb380ec388ec3": "DirectNodeServer:default -h 127.0.0.1 -p 10002"
    }
}
```

配置文件说明：

* `invitor`    计算邀请方钱包地址。parties 之一
* `parties`    数据提供方钱包地址列表，目前只支持两方参与。
* `method-price`    为每个函数进行定价，示例中将函数`YaoMillionairesProblem`定价为 **200000**
* `profit-rules`    指定计算结束后的分配规则，暂时按计算人数等比例分配
* `urls`    指定计算参与方MPC服务地址信息。其值为json对象，json对象的 key 为计算参与方地址；json对象的 value 为字符串，用来设置MPC服务地址信息，其中 `DirectNodeServer` 固定，`-h` 和 `-p` 分别为节点地址和 MPC 服务端口。MPC端口在[这里](zh-cn/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#为节点启用MPC功能)设定。

编译命令：

```shell
$ ../plang ./YaoMillionairesProblem.cpp -config ./config.json -I ./include -mpcc YaoMillionairesContract.cpp
```

终端输出：

```
debug digest:
 * IR NAME: MPCYaoMillionairesProblem
 * IR HASH: be4bfbbc708c5622e0ff6f274020d654
 * <p>
 * IR FUNC HASH(MD5)                 IR FUNC NAME            IR FUNC PROT
 * 0588f14217b11e0f77e50d03a88ba866  YaoMillionairesProblem  

digest:
 * IR NAME: MPCYaoMillionairesProblem
 * IR HASH: be4bfbbc708c5622e0ff6f274020d654
 * <p>
 * IR FUNC HASH(MD5)                 IR FUNC NAME            IR FUNC PROT
 * 0588f14217b11e0f77e50d03a88ba866  YaoMillionairesProblem  YaoMillionairesProblem(int,int)
```

输出文件如下:

```
├── code
│   └── java
│       ├── MPCYaoMillionairesProblem.java              # 给数据提供方使用
│       ├── MPCYaoMillionairesProblem-README.TXT
│       ├── ProxyYaoMillionairesProblem.java            # 给计算发起方使用
│       └── ProxyYaoMillionairesProblem-README.TXT
└── YaoMillionairesContract.cpp                         # 生成WASM合约，最终上链的合约文件
```

更多 `plang` 编译器使用详情，参考 [**Usage**](https://github.com/PlatONnetwork/privacy-contract-compiler#Usage)

**注意：配置文件或隐私合约代码的任何更新，都需要重新编译才能生效。**

### 隐私合约发布

隐私合约作为提供隐私计算的链上服务，最终想要发布到 `PlatON` 网络，需先编译成 [WASM合约](zh-cn/[Chinese-Simplified]-Wasm%e5%90%88%e7%ba%a6%e5%bc%80%e5%8f%91%e6%8c%87%e5%8d%97#Wasm%e5%90%88%e7%ba%a6%e7%ae%80%e8%bf%b0) 文件，然后通过编译、发布 WASM 合约的方式才能最终实现隐私合约发布上链。

下面简要说明在 Ubuntu 上发布编译生成的 Wasm 合约 `YaoMillionairesContract`，完成隐私合约发布流程。

1. 下载 Ubuntu 版本 [Wasm合约开发包](https://download.platon.network/0.3/pwasm-linux-amd64-0.3.0.tar.gz) 并解压到 ${pWasm} 。若已下载，跳至下一步。

2. 进入 ${pWasm} 目录，执行以下命令生成Wasm合约项目 `YaoProblem`

```bash
$ cd {pWasm}
$ ./script/autoproject.sh . YaoProblem
```

3. 拷贝隐私合约编译后的文件 `YaoMillionairesContract.cpp` 内容到 `{pWasm}/user/YaoProblem/YaoProblem.cpp`，然后进入 `{pWasm}/build` 编译

```bash
$ cp YaoMillionairesContract.cpp ${pWasm}/user/YaoProblem/YaoProblem.cpp
$ cd {pWasm}/build
$ make
```

4. 创建 `config.json` 配置文件，配置当前合约发布的信息：

```bash
{
  "url":"http://127.0.0.1:6789",
  "gas":"0x99999788888",
  "gasPrice":"0x333330",
  "from":"0x9a568e649c3a9d43b7f565ff2c835a24934ba447"
}
```
说明：发布合约可以是由任意账户发起。

5. 连接 `http://127.0.0.1:6789` 节点，解锁 `0x9a568e649c3a9d43b7f565ff2c835a24934ba447` 账户

```bash
$ platon 
> personal.unlockAccount("your-account")
Unlock account 0x9a568e649c3a9d43b7f565ff2c835a24934ba447
Passphrase:
true
```

6. 发布 Wasm 合约

```bash
$ chmod u+x ctool
$ ./ctool deploy --abi ./YaoProblem.cpp.abi.json --code ./YaoProblem.wasm --config ./config.json
```

假定合约发布后的合约地址为：
> 0x43355c787c50b647c425f594b441d4bd75198888

**更多 `Ubuntu` 下 `Wasm合约` 详情请参考** [Wasm合约开发指南](zh-cn/[Chinese-Simplified]-Wasm%e5%90%88%e7%ba%a6%e5%bc%80%e5%8f%91%e6%8c%87%e5%8d%97#%e7%bc%96%e8%af%91%e5%90%88%e7%ba%a6)。

### 隐私数据服务实现

通过引入隐私数据服务SDK(`mpc-data-sdk`)，数据提供方可以实现将自己的隐私数据服务注册到 `MPC VM` 并提供计算数据，更多接口详情参考[隐私数据服务SDK](#隐私数据服务SDK)。

**隐私数据提供方 & MPC计算节点：**

MPC计算节点上保存隐私数据提供方的钱包文件，数据提供方的隐私数据服务必须注册到这个节点的 `MPC VM` 中。因为隐私计算为多方计算，而目前只有两方计算，以 `Alice` 和 `Bob` 区分。编译隐私合约时，已配置 `Alice` 方地址为 `0x9a568e649c3a9d43b7f565ff2c835a24934ba447`，`Bob` 方地址为`0xce3a4aa58432065c4c5fae85106aee4aef77a115`。

**步骤一：编写隐私数据服务代码**

目前 `mpc-data-sdk` 只提供 `JAVA` 版本，因此读者需具备一定的 JAVA 编程基础。以下步骤将以最简方式演示如何接入`mpc-data-sdk`，部分编码需要手工完成。

* 创建 JAVA 普通 Maven 工程，修改`pom.xml`，添加maven仓库地址

```xml
<repositories>
   <repository>
        <id>juzixmaven</id>
        <name>juzix maven</name>
        <url>https://sdk.platon.network/nexus/content/groups/public/</url>
    </repository>
</repositories>
```

* 引入`mpc-data-sdk`依赖

```xml
<dependency>
    <groupId>net.platon.mpc</groupId>
    <artifactId>mpc-data-sdk</artifactId>
    <version>1.0</version>
</dependency>
```

* 代码实现

在工程中新建 `net.platon.vm.mpc` 包，将 `plang` 编译隐私合约后生成的 JAVA 代码 `MPCYaoMillionairesProblem.java` 放入包中。**不要修改包名！！** 接下来实现`inputImpl`方法。

Alice方只需要实现 `MPCYaoMillionairesProblem_YaoMillionairesProblem_int_int_01` 类中的方法，代码实现如下：

```java
/**
 * YaoMillionairesProblem(int,int)
 */
final class MPCYaoMillionairesProblem_YaoMillionairesProblem_int_int_01 extends mpc_f_0588f14217b11e0f77e50d03a88ba866_01 {
    public byte[] inputImpl(final InputRequestPara para) {
        int ret_value = 0;
        // TODO: assemble data

        ret_value = 1000000; // Alice 1 million

        return Data.Int32(ret_value);
    }
}
```

Bob方只需要实现 `MPCYaoMillionairesProblem_YaoMillionairesProblem_int_int_02` 类中的方法，代码实现如下：

```java
/**
 * YaoMillionairesProblem(int,int)
 */
final class MPCYaoMillionairesProblem_YaoMillionairesProblem_int_int_02 extends mpc_f_0588f14217b11e0f77e50d03a88ba866_02 {
    public byte[] inputImpl(final InputRequestPara para) {
        int ret_value = 0;
        // TODO: assemble data

        ret_value = 2000000; // Bob 2 million

        return Data.Int32(ret_value);
    }
}
```

文件中出现文件名与方法名恰好一样并且都还不短，但这是符合命名规范的。所以不要纠结类名或方法名太长，再次强调 **不要修改类名！！** 类名格式说明：

`MPCYaoMillionairesProblem_YaoMillionairesProblem_int_int_01` 

	- MPCYaoMillionairesProblem        MPC前缀 + 隐私合约中算法函数名
	- YaoMillionairesProblem           方法名
	- int_int                          两个输入参数的类型
	- 01                               数据提供方编号，Alice为01，Bob为02

`mpc_f_0588f14217b11e0f77e50d03a88ba866_01`

	- mpc_f                             特定前缀
	- 0588f14217b11e0f77e50d03a88ba866  方法 YaoMillionairesProblem 的MD5值,这里使用MD5是为了以后使用函数重载考虑的
	- 01                                数据提供方编号，Alice为01，Bob为02

**注意：数据提供方务必实现代理类中的 `InputImpl` 方法，否则计算时拿到的数据都是相关类型的默认值。**

* 定义程序入口

新增应用程序入口类，该入口函数用于在启动应用程序时接受参数并启动服务：

```java
public class Client {
    public static void main(String[] args) {
        ConfigInfo cfgInfo = new ConfigInfo(args);
        // 可以在此处对cfgInfo中的参数进行硬编码
        // cfgInfo.walletPath=""
        // cfgInfo.walletPass=""
        // cfgInfo.iceCfgFile=""
        AppClient app = new AppClient(cfgInfo);
        app.start(args);
    }
}
```

程序启动需要传入3个参数，这些参数可以通过 `args`系统变量传入，也可以在 `main` 函数中通过硬编码指定。

`main` 函数入参数说明：

| 参数 | 说明 | 可空 |
| :---------- | :----------- | :---- |
| walletPath | 隐私数据提供方钱包文件全路径 | 否   |
| walletPass | 账户密码  | 否   |
| iceCfgFile | ICE配置文件（`cfg.client1.config`）所在全路径 | 否   |

**步骤二：配置相关文件**

`Alice` 方的 `cfg.client1.config` 配置如下：

```
TaskCallback.Proxy=tasksession:default -h 127.0.0.1 -p 10001
Callback.Client.Endpoints=default -h 10.10.8.163
```

`Bob` 方的 `cfg.client2.config` 配置如下：

```
TaskCallback.Proxy=tasksession:default -h 127.0.0.1 -p 10002
Callback.Client.Endpoints=default -h 10.10.8.163
```

配置文件说明：

- MPC 计算过程中需要两个数据提供方（`Alice` & `Bob`）同时启动隐私数据服务，但 `Alice` 方和 `Bob` 方的启动没有先后顺序。
-  `TaskCallback.Proxy` 的地址和端口与[启动带MPC计算功能](zh-cn/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#%e4%b8%ba%e8%8a%82%e7%82%b9%e5%90%af%e7%94%a8MPC%e5%8a%9f%e8%83%bd)的 PlatON 节点时，参数 `--mpc.ice` 指定的 ICE 配置文件中配置的地址和端口保持一致
- 如果启动节点的时候没有指定 `--mpc.ice` 的配置，则默认端口是`8201`
- `Callback.Client.Endpoints`为启动数据隐私服务所在的服务器地址

**步骤三：打包及运行**

可以将该 maven 项目打成可执行的 jar 包运行，也可直接执行main函数，我们以 jar 包的形式运行，命令如下：

Alice方：

```bash
$ java -jar mpc-data-sdk-client1-1.0-SNAPSHOT.jar --walletPath=./config/9a568e649c3a9d43b7f565ff2c835a24934ba447 --walletPass=11111111 --iceCfgFile=./config/cfg.client1.config
```

Bob方：

```bash
$ java -jar mpc-data-sdk-client2-1.0-SNAPSHOT.jar --walletPath=./config/ce3a4aa58432065c4c5fae85106aee4aef77a115 --walletPass=11111111 --iceCfgFile=./config/cfg.client2.config
```

上述命令会让程序启动时与 MPC 计算节点进行连接。需要注意的是，只有数据提供方与各自的 `MPC` 计算节点都连接成功，才能进行下一步操作。连接成功之后在各自节点 `log` 目录下 `platon_mpc_XXXX.log` 日志中能看到以下信息，其中 `XXXX` 为节点进程号：

```
[mpc_task_session_impl.cpp:15:registTransactionCallback] ##  user_address:0x9a568e649c3a9d43b7f565ff2c835a24934ba447, registTransactionCallback
[mpc_task_session_impl.cpp:26:registerIR]    ## ir_hash:0x9a568e649c3a9d43b7f565ff2c835a24934ba447, registerIR
```

```
[mpc_task_session_impl.cpp:15:registTransactionCallback]##  user_address:0xce3a4aa58432065c4c5fae85106aee4aef77a115, registTransactionCallback 
[mpc_task_session_impl.cpp:26:registerIR]	## ir_hash:0xce3a4aa58432065c4c5fae85106aee4aef77a115, registerIR
```

### 隐私客户端实现

通过引入隐私客户端SDK(`mpc-proxy-sdk`)，用户可以构建自己隐私客户端来发起计算，查询计算结果等，SDK接口详情参考[隐私客户端SDK](#隐私客户端SDK)

前置条件：
1. 已正常运行两个MPC计算节点；
2. 隐私数据服务程序已与各计算节点相连成功；
3. 准备好计算发起方的钱包及密码；
4. 已获得`WASM`合约在链上的合约地址；

在确认上述四个条件都已经满足之后，开始以下流程：

**步骤一：隐私客户端实现代码**

- 创建 JAVA 普通 Maven 工程，修改`pom.xml`，添加maven仓库地址

```xml
<repositories>
   <repository>
        <id>juzixmaven</id>
        <name>juzix maven</name>
        <url>https://sdk.platon.network/nexus/content/groups/public/</url>
    </repository>
</repositories>
```

- 引入`mpc-proxy-sdk`依赖。

```xml
<dependency>
    <groupId>net.platon.mpc</groupId>
    <artifactId>mpc-proxy-sdk</artifactId>
    <version>1.0</version>
</dependency>
```

- 代码实现

在工程中新建 `platon.mpc.proxy` 包，将`plang`编译之后生成 JAVA 代码中`ProxyYaoMillionairesProblem.java`放入包中，**不要修改包名！！** 创建程序入口，main函数实现如下：

```java
public static void main(String[] args) {
    /*
    * 0. 需要提供以下参数
    */
    //计算发起方钱包文件，这里由 Alice 方发起请求
    String walletPath = "config/9a568e649c3a9d43b7f565ff2c835a24934ba447";
    //钱包密码
    String walletPass = "11111111";
    //任意节点的RPC端口地址
    String url = "http://127.0.0.1:6789";
    //发布的对应WASM合约地址
    String contractAddress = "0x43355c787c50b647c425f594b441d4bd75198888";

    /*
    * 1. 新建一个客户端
    */
    ProxyYaoMillionairesProblem client = new ProxyYaoMillionairesProblem(url, walletPath, walletPass);
    client.setContractAddress(contractAddress);

    /*
    * 2. 发起计算，并获取计算交易hash,如果失败会重试3次
    */
    String transactionHash = client.startCalc(ProxyYaoMillionairesProblem.Method.boolean_YaoMillionairesProblem_int_int, 3);

}
```

**步骤二：发起计算请求**

在此步骤为便于调试，可在 IDE 端直接运行main函数发起计算，此时在控制台中会输出发起计算的交易hash:

```
transaction hash: 0xa7423e579c6a6bbbc57d6201c6bef3f09944bad78c7036f0108fa27daef5ff6c
```

计算过程需要出20个块后才有日志输出和结果，计算成功后，在两方节点 `log` 目录下 `platon_mpc_XXXX.log` 日志中能看到以下信息，其中 `XXXX` 为节点进程号：

```
===================================================
        taskId: 54396487fef9eb7e3a098755ed88e187866e6d2032aba55592a1ba2e0ab7b4e8
        pubkey: a363d1243646b6eabf1d4851f646b523f5707d053caab95022f1682605aca0537ee0c5c14b4dfa76dcbce264b7e68d59de79a42b7cda059e9d358336a9ab8d80
        irhash: e12d98408141b5fc94d84830465db225
        method: YaoMillionairesProblem
        extras: 
        txaddr: 0x60Ceca9c1290EE56b98d4E160EF0453F7C40d219
        iraddr: 0x43355C787c50b647C425f594b441D4BD751951C1
       invitor: yes
          role: ALICE
        irpath: data/ir//e12d98408141b5fc94d84830465db225.data
   method_hash: 0588f14217b11e0f77e50d03a88ba866
     transhash: 
        status: PROCESS_OK
        errmsg: OK
===================================================
```

```
===================================================
         taskId: 54396487fef9eb7e3a098755ed88e187866e6d2032aba55592a1ba2e0ab7b4e8    
         pubkey: a363d1243646b6eabf1d4851f646b523f5707d053caab95022f1682605aca0537ee0c5c14b4dfa76dcbce264b7e68d59de79a42b7cda059e9d358336a9ab8d80
         irhash: e12d98408141b5fc94d84830465db225
         method: YaoMillionairesProblem
         extras: 
         txaddr: 0x60Ceca9c1290EE56b98d4E160EF0453F7C40d219  
         iraddr: 0x43355C787c50b647C425f594b441D4BD751951C1  
        invitor: no
           role: BOB
         irpath: data/ir//e12d98408141b5fc94d84830465db225.data
    method_hash: 0588f14217b11e0f77e50d03a88ba866
      transhash: 0x675cf6dfaea7901f5116b1b6458af3a351b0b41962ebb27544f7dbe2d6a7fd80
         status: PROCESS_OK
         errmsg: OK
 ===================================================
```
**注意：只有在两方均 `PROCESS_OK` 才算计算成功。**

**步骤三：查询结果**

可通过sdk中提供的方法获取，代码实现如下：

```java
public static void main(String[] args) {
    /*
    * 0. 需要提供以下参数
    */
    //计算发起方钱包文件，这里由 Alice 方发起请求
    String walletPath = "config/9a568e649c3a9d43b7f565ff2c835a24934ba447";
    //钱包密码
    String walletPass = "11111111";
    //任意节点的RPC端口地址
    String url = "http://127.0.0.1:6789";
    //发布的对应WASM合约地址
    String contractAddress = "0x43355c787c50b647c425f594b441d4bd75198888";

    /*
    *  新建一个客户端
    */
    ProxyYaoMillionairesProblem client = new ProxyYaoMillionairesProblem(url, walletPath, walletPass);
    client.setContractAddress(contractAddress);

   /*
   * 通过发起计算交易hash,查询结果，超时1s
   * transactionHash 为第二步显示在控制台的交易 Hash
   */
   String cipher = client.getResultByTransactionHash(transactionHash, 1000);
        if (cipher != null) {
            boolean b = client.getBool(cipher);
            logger.info("Client1 result : {} richer", b ? "alice" : "bob");
        } else {
            logger.info("try later!");
        }
   }
}
```

通过交易 hash 返回的结果 `cipher` 是密文，`getBool(cipher)` 方法会将密文解密，然后转换为相应类型。

**注意：只能由上一步中的计算发起方解密获取明文，其他人只能获取密文，用 `getBool(cipher)` 方法转换会失败。**

查询交易回执：

```
{
  blockHash: "0x7b59c274d6e4e67cbee9e92e754ada44e6ff88fe4de632680758a3d6eb9eecc0",
  blockNumber: 4977,
  contractAddress: null,
  cumulativeGasUsed: 112767,
  from: "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219",
  gasUsed: 112767,
  logs: [{
      address: "0xb136a7190dd23c57c1d8f07011174af3bbe3cfe2",
      blockHash: "0x7b59c274d6e4e67cbee9e92e754ada44e6ff88fe4de632680758a3d6eb9eecc0",
      blockNumber: 4977,
      data: "0xf84301b84064396361306232363866376163336339323732313565666234636335653038393339336631376631663336363765656666313664396536653836313866353137",
      logIndex: 0,
      removed: false,
      topics: ["0x9938524d0b7638d8cb06ef86cc46ae3074007aea703ce237efe1470b6bdb5f31"],
      transactionHash: "0xa7423e579c6a6bbbc57d6201c6bef3f09944bad78c7036f0108fa27daef5ff6c",
      transactionIndex: 0
  }],
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000020000000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000004000000000000000000000000000000000000000000000000000000000000000000000800000000000000000000000000000000",
  status: "0x1",
  to: "0xb136a7190dd23c57c1d8f07011174af3bbe3cfe2",
  transactionHash: "0xa7423e579c6a6bbbc57d6201c6bef3f09944bad78c7036f0108fa27daef5ff6c",
  transactionIndex: 0
}
```

在获取的结果回执中，如果logs中为空，则说明任务执行失败！更多细节与描述参考完整工程[mpc-proxy-sdk](https://github.com/PlatONnetwork/privacy-contract-sdk/blob/master/README.md)。


## 隐私合约

**隐私合约**是 [PlatON](zh-cn/basics/[Chinese-Simplified]-快速指南) 上一种特殊的 MPC 合约，使用`C++`语言进行开发，运行在 PlatON 节点的MPC计算虚拟机（MPC VM）里。隐私合约借助 PlatON 平台安全多方计算（MPC）的能力，实现了对计算数据的隐私保护。

**注意**：当前版本仅提供两方安全计算的隐私合约编写的规范，三方及多方隐私计算计算版本将陆续推出。

### 隐私计算对象

PlatON底层封装了对隐私计算对象，用来承接隐私数据，目前仅仅支持整型隐私计算对象，即**emp::Integer**，并提供了一些构造方法，将基础数据类型转化为隐私对象，然后对隐私对象进行运算操作实现隐私计算逻辑。 
- 构造方法：

```
Integer(const int8_t& value, int party = PUBLIC);
Integer(const int16_t& value, int party = PUBLIC);
Integer(const int32_t& value, int party = PUBLIC);
Integer(const int64_t& value, int party = PUBLIC);
	
//长整数构造，length表示位数，str表示十进制整数
Integer(int length, const string& str, int party = PUBLIC);
```

在提供的构造方法中，支持将8位、16位、32位、64位整型以及其他任意长度的整型转化为隐私计算对象，并指定对应的参与方，目前参与方只能为`emp::ALICE`或`emp:BOB`。 如声明32位有符号整型隐私计算对象方式如下：

```cpp
int32_t in=123;
emp::Integer v1(in, emp::ALICE); //声明Alice 隐私计算变量
emp::Integer v2(in, emp::BOB);   //声明Bob 隐私计算变量
```
- 操作符重载：

```
//位运算
Integer operator<<(int shamt)const;
Integer operator>>(int shamt)const;
Integer operator<<(const Integer& shamt)const;
Integer operator>>(const Integer& shamt)const;
	
//算术
Integer operator+(const Integer& rhs)const;
Integer operator-(const Integer& rhs)const;
Integer operator-()const;
Integer operator*(const Integer& rhs)const;
Integer operator/(const Integer& rhs)const;
Integer operator%(const Integer& rhs)const;
	
//逻辑运算
Integer operator&(const Integer& rhs)const;
Integer operator|(const Integer& rhs)const;
Integer operator^(const Integer& rhs) const;
```

对整型隐私对象`emp::Integer`提供了位运算、算术运算和逻辑运算的接口，使用方式如下：

```
int32_t in=123;
emp::Integer v1(in, emp::ALICE); 
emp::Integer v2(in, emp::BOB);  
emp::Integer ret1 = v1 << 1;     
emp::Integer ret2 = v1 + v2;      
emp::Integer ret3 = (v1 & v2);
```

- 运算方法：

```
//比较emp::Integer大小
Bit geq(const Integer & rhs) const;
//判断两个emp::Integer是否相等
Bit equal(const Integer & rhs) const;
//将emp::Integer取绝对值
Integer abs() const;
```

对整型隐私对象`emp::Integer`提供了比较大小和绝对值运算接口，如比较两个整型隐私对象的差值做绝对值运算如下：

``` 
int32_t in=123;
emp::Integer v1(in, emp::ALICE); 
emp::Integer v2(in, emp::BOB);  
emp::Integer ret1 = (v1 - v2).abs()
```

- 取值

```
string reveal_string(int party = PUBLIC) const;
int64_t reveal(int party = PUBLIC) const;
uint64_t reveal_uint(int party = PUBLIC) const;
```

用于取出隐私对象中的值，转为相应的基础类型，使用方式如下：

```
int32_t in=123;
emp::Integer v1(in, emp::ALICE); 
emp::Integer v2(in, emp::BOB); 
emp::Integer ret1 = (v1 - v2).abs()
int32_t ret = ret1.reveal()
```

- 其他

```
int size() const;                        //计算隐私对象的位数
Bit& operator[](int index);              //取index上的值
const Bit & operator[](int index) const; //取index上的值
```

完整的隐私计算对象`emp::Integer`头文件可在[这里](https://github.com/kelvinskk/emp-tool/blob/8d86e41a8f5e234465698fe4da4a6730cf4e0cee/include/integer.h)查看。

***注意*** :
- 隐私计算运算类型变量运算要求Alice和Bob在同类型、同长度的条件下进行，否则计算报错。
- 隐私计算运算类型变量运算要求计算双方，一个为Alice角色，另一个为Bob角色，否则计算无法进行。

### 隐私计算函数

计算逻辑的实现函数，输入参数为2个，参数1为Alice的输入，参数2为Bob的输入，暂时不支持更多参数。

函数定义原型：

```cpp
return_type function_name(declare_type in1, declare_type in2)
```

参数说明：

```
in1:           Alice方数据
in2:           Bob方的数据
declare_type:  入参类型
return_type:   返回类型
```

**注意：**

1. 入参类型和返回类型要求：
   - C++基础类型：char, unsigned, int, short, long, char, float, double等；
   - 字符串： std::string ；
   - 自定义类型：[protobuf](#依赖组件) 定义的消息类型；
   
2. 输入参数和返回值全部使用 [protobuf](#依赖组件) 编码，包括基础类型，string，自定义类型；
  
3. 在[MPC Java SDK] 的数据服务程序中，已经完成了基本数据类型的 `protobuf` 打包和解包处理。

### 使用范例

- 范例一：编写一个输入和返回为基础类型的隐私计算函数如下：

```cpp
#include "integer.h"    

/**
* 比较Alice与Bob输入的大小
*/
bool YaoMillionairesProblem(int in1, int in2) {
    emp::Integer alice(in1, emp::ALICE);  	// Alice输入
    emp::Integer bob(in2, emp::BOB);       	// Bob输入
    int ret = (alice - bob).reveal();      	// 执行比较大小，并获取比较结果
    return ret >= 0;
}
```

- 范例二：编写一个带有`protobuf`自定义类型的隐私计算函数如下：

```
#include "msg.pb.h"
#include "integer.h"   

/**
* 对Alice输入的Foo对象字段item1、item2分别和Bob的输入值in2进行绝对值运算，返回计算后的Foo对象
*/
Foo foo_abs(const Foo& in1, int32_t in2){
    Foo Foo = in1;
    emp::Integer item1(in1.item1(), emp::ALICE);    //Alice输入item1
    emp::Integer item2(in1.item2(), emp::ALICE);    //Alice输入item2
    emp::Integer v2(in2, emp::BOB);                 //Bob输入
    emp::Integer ret1 = (item1 - v2).abs();         //将Alice输入item1与Bob的输入做隐私计算后取绝对值
    emp::Integer ret2 = (item2 - v2).abs();         //将Alice输入item2与Bob的输入做隐私计算后取绝对值
    foo.set_item1(ret1.reveal());                   //重置item1
    foo.set_item2(ret2.reveal());                   //重置item2
    return foo;                                     
}
```

其中Foo定义如下：msg.proto

```protobuf
syntax = "proto3";
message Foo {
    int32 item1 = 1;
    int32 item2 = 2;
    string info = 3;
};
```

在隐私合约中使用自定义类型的引用之前，需要将`proto`文件编译生成`msg.pb.h`、`msg.pb.cc`、`Msg.java` 三个文件，其中`msg.pb.h`作为隐私合约编写的头文件，`msg.pb.cc`、`Msg.java` 在 `plang` 编译隐私合约时分别通过参数`-protobuf-cc`、`-protobuf-java`指定参与编译，且`Msg.java`在数据输入端和计算发起端时候需要引入。`proto`编译方式如下：

```bash
$ protoc msg.proto --cpp_out=./ --java_out=./ -I{path/to/protobuf/include}
```

其中 `{path/to/protobuf/include}`需替换为`protobuf`安装头文件路径。

## 隐私数据服务SDK

隐私数据服务SDK提供给隐私数据参与方使用，基于该SDK可以为MPC计算提供数据源，并最终获取计算上链，使用隐私数据服务SDK，只需要实现MPC回调接口类即可。

### MPC回调接口

```java
public interface MpcCallbackInterface {
    public byte[] input(final InputRequestPara para);                    // data input
    public void error(final InputRequestPara para, ErrorCode error);     // error notify
    public void result(final InputRequestPara para, final byte[] data);  // result notify
}
```
在用`plang`生成的客户端代码中会生成该接口的实现类，如下：

```
abstract class MpcCallbackBase_fbbf2d8c40e87f406991b1d40bdd94dd implements MpcCallbackInterface {
        public abstract byte[] inputImpl(final InputRequestPara para);
        
        public byte[] input(final InputRequestPara para) {
            // TODO: do what you want to do, before call inputImpl
            return inputImpl(para);
        }

        public void error(final InputRequestPara para, ErrorCode error) {
            // TODO: do what you want to do

        }

        public void result(final InputRequestPara para, final byte[] data) {
            // TODO: do what you want to do
        }
 }
```

方法使用说明：

| 方法名  | 方法说明 |
| ------------- | ------------- |
| input  | 通过 inputImpl 方法传入数据参与计算的数据，可以直接通过 ret_value 给基础类型赋值，或者通过builder.set_XXX 给 message 结构体进行赋值 |
| error  | 计算过程出现的错误会通过该接口返回，用户可以在这里对错误信息进行处理 |
| result  | 目前在计算参与Bob方能够通过该接口获得计算结果，其中`data`是明文的16进制串 |

更多示例参考[MPCSamples.java](privacy-contract/samples/MPCSamples.java)

## 隐私客户端SDK

说明：以下涉及的方法均在由 `plang` 编译获得代理类 `ProxyXXX.java` 中使用。

### 发起计算

```java
public String startCalc(Method method)
public String startCalc(Method method, int retry)
```
函数功能：

通过该方法发起计算，并返回交易hash，根据该交易hash，可以获取计算任务ID、交易回执、结果密文等。

参数说明：

* `method`： 发起计算调用的方法，Method定义在生成的代理类中
* `retry`： 重发次数，如果返回为null,会从发交易，直到返回的不为空，或者达到重发次数；默认retry为0
* 返回值：交易hash

### 获取任务ID

```java
public String getTaskId(String transactionHash)
public String getTaskId(String transactionHash, long timeout)
```
函数功能：

根据交易hash，获取计算任务Id，后续可以通过任务Id查询结果。

参数说明：
* `transactionHash`：发起计算方法`startCalc`返回的交易哈希
* `timeout`：超时时间，最小0，最大180s
* 返回值：任务ID

### 获取交易回执

```java
public TransactionReceipt getTransactionReceipt(String transactionHash);
```

函数功能：
 * 根据交易hash，获取交易回执。

参数说明：
* `transactionHash`：发起计算方法 `startCalc` 返回的交易哈希
* 返回值：交易回执。如果计算成功，返回的交易回执中 `logs` 参数不为空，否则表示计算失败。`logs` 实例如下：

```
logs: [{
      address: "0xb136a7190dd23c57c1d8f07011174af3bbe3cfe2",
      blockHash: "0x7b59c274d6e4e67cbee9e92e754ada44e6ff88fe4de632680758a3d6eb9eecc0",
      blockNumber: 4977,
      data: "0xf84301b84064396361306232363866376163336339323732313565666234636335653038393339336631376631663336363765656666313664396536653836313866353137",
      logIndex: 0,
      removed: false,
      topics: ["0x9938524d0b7638d8cb06ef86cc46ae3074007aea703ce237efe1470b6bdb5f31"],
      transactionHash: "0x9bbf80f7d976e422472bd3cb3b96eb9fc71116c581da31da5102928db3fe4db3",
      transactionIndex: 0
  }],
```

### 获取结果密文

```java
public String getResultByTransactionHash(String transactionHash)
public String getResultByTransactionHash(String transactionHash, long timeout)
public String getResultByTaskId(String taskId)
public String getResultByTaskId(String taskId, long timeout)
```

函数功能：
 * 根据交易hash或者任务ID，查询计算结果，结果是用计算发起方的公钥加密(ECIES)后的密文。

参数说明：
* `transactionHash`：发起计算方法 `startCalc` 返回的交易哈希
* `taskId`： `getTaskId`返回的任务ID
* `timeout`：超时时间，最小0，最大180s
* 返回值：计算结果密文

### 获取结果明文

```java
public int getInt32(String cipher)
public long getInt64(String cipher)
// getUInt32 getUInt64 getBool getFloat getDouble getString ... 
public com.abc.sample.Samples.Foo getFoo(String cipher)
// ...
```

函数功能：

该类接口将获取到的密文进行解密之后转换为相应的基本类型，或者自定义的 `protobuf` 结构体。其中基本类型的的转换在 `mpc-proxy-sdk` 中已经进行封装，而自定义的 `protobuf` 结构体的转换在 `plang` 编译获得的 JAVA 文件中生成，可以直接使用。

参数说明：
* `cipher`： 密文字符串。
* 返回值：基本类型或结构体

**注意：由于该方法中需要将密文解析为明文，需要计算发起方才能通过该方法获取明文，非计算发起方调用此方法会因无法解密而报错！**

### 其他

```java
public static void showMethodMap()
```

函数功能：     

显示函数方法名，函数原型，函数枚举。

```java
public String getPlainText(String cipher)
```

函数功能：     

传入密文，获取结果明文(16进制字符串)。这个在知道私钥和密文的情况下即可使用。