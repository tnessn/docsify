-   [概览](#概览)
-   [版本说明](#版本说明)
    -   [v0.2.0 更新说明](#v0.2.0-更新说明)
-   [快速入门](#快速入门)
    -   [安装或引入](#安装或引入)
    -   [初始化代码](#初始化代码)
-   [合约](#合约)
    -   [合约示例](#合约示例)
    -   [合约骨架生成](#合约骨架生成)
    -   [加载合约](#加载合约)
    -   [部署合约](#部署合约)
    -   [合约call调用](#合约call调用)
    -   [合约sendRawTransaction调用](#合约sendrawtransaction调用)
    -   [合约event](#合约event)
-   [web3](#web3)
    -   [web3 eth相关 (标准JSON RPC )](#web3-eth相关-标准json-rpc)
    -   [新增的接口](#新增的接口)
        -   [ethPendingTx](#ethpendingtx)

# 概览
> Java SDK是PlatON面向java开发者，提供的PlatON公链的java开发工具包

# 版本说明

## v0.2.0 更新说明

1. 支持PlatON的智能合约

# 快速入门

## 安装或引入

### 环境要求

1. jdk1.8

### maven
1. 仓库地址 https://sdk.platon.network/nexus/content/groups/public/
2. maven方式引用
```
<dependency>
    <groupId>com.platon.client</groupId>
    <artifactId>core</artifactId>
    <version>x.x.x</version>
</dependency>
```
3. gradle方式引用
```
compile "com.platon.client:core:x.x.x"
```
### 合约骨架生成工具
1. 安装包下载 https://download.platon.network/client-sdk.zip
2. 解压后说明
```
.
+-- _bin
|   +-- client-sdk.bat                 //windows执行程序
|   +-- client-sdk                     //linux执行程序
+-- _lib
|   +-- xxx.jar                        //类库
|   +-- ...
```
3. 到bin目录执行 ./client-sdk

```
              _      _____ _     _
             | |    |____ (_)   (_)
__      _____| |__      / /_     _   ___
\ \ /\ / / _ \ '_ \     \ \ |   | | / _ \
 \ V  V /  __/ |_) |.___/ / | _ | || (_) |
  \_/\_/ \___|_.__/ \____/| |(_)|_| \___/
                         _/ |
                        |__/

Usage: client-sdk version|wallet|solidity|truffle|wasm ...
```

## 初始化代码
```
Web3j web3 = Web3j.build(new HttpService("http://localhost:6789"));
```

# 合约

## 合约示例

```
namespace platon {
    class ACC : public token::Token {
    public:
        ACC(){}
        void init(){
            Address user(std::string("0xa0b21d5bcc6af4dda0579174941160b9eecb6916"), true);
            initSupply(user, 199999);
        }

        void create(const char *addr, uint64_t asset){
            Address user(std::string(addr), true);
            Token::create(user, asset);
        }

        uint64_t getAsset(const char *addr) const {
            Address user(std::string(addr), true);
            return Token::getAsset(user);
        }

        void transfer(const char *addr, uint64_t asset) {
            Address user(std::string(addr), true);
            Token::transfer(user, asset);
        }
    };
}

PLATON_ABI(platon::ACC, create)
PLATON_ABI(platon::ACC, getAsset)
PLATON_ABI(platon::ACC, transfer)
//platon autogen begin
extern "C" { 
    void create(const char * addr,unsigned long long asset) {
        platon::ACC ACC_platon;
        ACC_platon.create(addr,asset);
    }
    unsigned long long getAsset(const char * addr) {
        platon::ACC ACC_platon;
        return ACC_platon.getAsset(addr);
    }
    void transfer(const char * addr,unsigned long long asset) {
        platon::ACC ACC_platon;
        ACC_platon.transfer(addr,asset);
    }
    void init() {
        platon::ACC ACC_platon;
        ACC_platon.init();
    }
}
//platon autogen end
```

## 合约骨架生成
1. wasm智能合约的编写及其ABI(wasm文件)和BIN(json文件)生成方法请参考 [wiki](https://github.com/PlatONnetwork/wiki/wiki)
2. 使用合约骨架生成工具
```
client-sdk wasm generate /path/to/token.wasm /path/to/token.cpp.abi.json -o /path/to/src/main/java -p com.your.organisation.name
```

## 加载合约
```
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Token contract = Token.load(binData, "0x<address>", web3j, credentials, new StaticGasProvider(GAS_PRICE, GAS_LIMIT));
```

## 部署合约
```
Web3j web3 = Web3j.build(new HttpService("http://localhost:6789"));
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Token contract = Token.deploy(web3, credentials, binData, new StaticGasProvider(GAS_PRICE,GAS_LIMIT)).send();
```

## 合约call调用
```
Request<?, org.web3j.protocol.core.methods.response.EthCall> req = web3j.ethCall(Transaction.createEthCallTransaction(
               "0xa70e8dd61c5d32be8058bb8eb970870f07233155",
               "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
                       "0x0"),
               DefaultBlockParameter.valueOf("latest"));
org.web3j.protocol.core.methods.response.EthCall res = req.send();
String value = res.getValue();

```

## 合约sendRawTransaction调用
```
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));

String signedData = "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675";
Request<?, org.web3j.protocol.core.methods.response.EthSendTransaction> req = web3j.ethSendRawTransaction(signedData);
org.web3j.protocol.core.methods.response.EthSendTransaction res = req.send();
String transactionHash = res.getTransactionHash();

```
## 合约event
假设合约中定义了名称为transfer的事件，则可以使用下面的代码监听：

```
String contractAddress = "0x223424fskljlsldfsf";
Event event = new Event("transfer", Arrays.asList(new TypeReference<Address>() {}));
EthFilter filter = new EthFilter(DefaultBlockParameterName.EARLIEST,
        DefaultBlockParameterName.LATEST, contractAddress);
filter.addSingleTopic(EventEncoder.encode(event));
web3j.ethLogObservable(filter).subscribe(log -> {
    System.out.println(log);
});
```

# web3
## web3 eth相关 (标准JSON RPC )
- java api的使用请参考[web3j github](https://github.com/web3j/web3j)

## 新增的接口
### ethPendingTx
>查询待处理交易

**参数**
 
 无
 
**返回值**

`Request<?, EthPendingTransactions>`
EthPendingTransactions属性中的transactions即为对应存储数据

**示例**
```
Request<?, EthPendingTransactions> req = web3j.ethPendingTx();
EthPendingTransactions res = req.send();
List<Transaction> transactions = res.getTransactions();
```
