# 目录

- [概览](#概览)
- [版本说明](#版本说明)
  - [v0.2.0 更新说明](#v020-更新说明)
- [快速入门](#快速入门)
  - [安装或引入](#安装或引入)
  - [初始化代码](#初始化代码)
- [合约](#合约)
  - [合约示例](#合约示例)
  - [部署合约](#部署合约)
  - [合约call调用](#合约call调用)
  - [合约sendRawTransaction调用](#合约sendRawTransaction调用)
- [web3](#web3)
  - [web3 eth相关 (标准JSON RPC )](#web3-eth相关-标准JSON-RPC)
  - [新增的接口](#新增的接口)
    - [contract](#contract)
      - [getPlatONData](#contract.getPlatONData)
      - [decodePlatONCall](#contract.decodePlatONCall)
      - [decodePlatONLog](#contract.decodePlatONLog)

## 概览
> Javascript SDK是PlatON面向js开发者，提供的PlatON公链的js开发工具包

## 版本说明

### v0.2.0 更新说明

1. 支持PlatON的wasm智能合约

### 快速入门

### 安装或引入

通过node.js引入：

`cnpm i https://github.com/PlatONnetwork/client-sdk-js`

### 初始化代码

然后你需要创建一个web3的实例，设置一个provider。为了保证你不会覆盖一个已有的provider，比如使用Mist时有内置，需要先检查是否web3实例已存在。


```js
if (typeof web3 !== 'undefined') {
    web3 = new Web3(web3.currentProvider);
} else {
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));
}


```

### 合约

wasm智能合约的编写及其ABI(wasm文件)和BIN(json文件)生成方法请参考 [wiki](https://github.com/PlatONnetwork/wiki/wiki)

#### 合约示例


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

#### 部署合约

##### 接口声明

    MyContract.deploy(deployData [, callback])

##### 参数说明

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|deployData |String |必选| 16进制格式的签名交易数据|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例


````js
const Web3 = require('web3'),
    fs = require('fs'),
    path = require('path'),
    Tx = require('ethereumjs-tx'),
    abi = require('./multisig.cpp.abi.json') //应用程序二进制接口
    ;

const provider = 'http://localhost:6789',
    privateKey='fd098d39e56115762cf28d7d223a4303a42a42451da6e7da37cb40a81a246d98',//私钥
    address='0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b'

const web3 = new Web3(new Web3.providers.HttpProvider(provider))

const MyContract  = web3.eth.contract(abi)

const source = fs.readFileSync(path.join(__dirname, './demo.wasm'));//WebAssembly的二进制代码 bin

const platONData = MyContract.deploy.getPlatONData(source)

const params = {
    nonce: web3.eth.getTransactionCount(address),
    gas: "0x506709",
    gasPrice: "0x8250de00",
    data: platONData,
    from:address
}

const daployData=sign(privateKey,params)

let myContractReturned = MyContract.deploy(daployData, (err, myContract)=> {
    if (!err) {
        if (!myContract.address) {
            console.log("contract deploy transaction hash: " + myContract.transactionHash) // 部署合约的交易哈希值
        } else {
            // 合约发布成功
            console.log("contract deploy transaction address: " + myContract.address)// 部署合约的地址
            const receipt = web3.eth.getTransactionReceipt(myContract.transactionHash);
            console.log(`contract deploy receipt:`, receipt);
        }
    } else {
        console.log(`contract deploy error:`, err)
    }
});

function sign(privateKey, data) {
    const key = new Buffer(privateKey, 'hex')
    const tx = new Tx(data)
    tx.sign(key)

    const serializeTx = tx.serialize()
    const result = '0x' + serializeTx.toString('hex')
    return result
}


````

#### 合约call调用

> 调用合约函数，返回常量值

##### 接口声明

    web3.eth.call(Object [, defaultBlock] [, callback])

##### 参数说明

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|Object |String |必选| 返回一个交易对象，同`web3.eth.sendTransaction`。与`sendTransaction`的区别在于，`from`属性是可选的|
|defaultBlock|Number/String |可选|如果不设置此值使用`web3.eth.defaultBlock`设定的块，否则使用指定的块|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例


````js
const data = contract.getOwners.getPlatONData()

const result = web3.eth.call({
    from: web3.eth.accounts[0],
    to: contract.address,
    data: data
});

console.log('call result:', result);

contract.decodePlatONCall(result)


````

#### 合约sendRawTransaction调用

> 发送通过私钥签名的交易

##### 接口声明

    web3.eth.sendRawTransaction(signedTransactionData [, callback])

##### 参数说明

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|signedTransactionData |String |必选|16进制格式的签名交易数据|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例


````js
const Tx = require('ethereumjs-tx');

const privateKey='4484092b68df58d639f11d59738983e2b8b81824f3c0c759edd6773f9adadfe7',
    param_from = web3.eth.accounts[0],
    param_to = wallet.address,
    param_assert = 4
    ;

const data = myContractInstance.transfer02.getPlatONData(param_from, param_to, param_assert);

const hash = web3.eth.sendRawTransaction(sign(privateKey,getParams(data)))
console.log('hash', hash);//0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f

const data=web3.eth.getTransactionReceipt(hash);

let res = myContractInstance.decodePlatONLog(data.logs[0]);

function sign(privateKey, data) {
    const key = new Buffer(privateKey, 'hex')
    const tx = new Tx(data)
    tx.sign(key)

    const serializeTx = tx.serialize()
    const result = '0x' + serializeTx.toString('hex')
    return result
}

function getParams(data = '', value = "0x0") {
    const nonce = web3.eth.getTransactionCount(wallet.address)
    value = web3.toHex(web3.toWei(value, 'ether'));

    const params = {
        from: '0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b',
        gasPrice: 22 * 10e9,
        gas: 80000,
        to: myContractInstance.address,
        value,
        data,
        nonce
    }

    return params
}


````

### web3

----

### web3 eth相关 (标准JSON RPC )
- api的使用请参考[web3j github](https://github.com/ethereum/wiki/wiki/JavaScript-API)

### 新增的接口

#### contract

##### 接口声明

    web3.eth.contract(abi)

##### 参数

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|abi|Array|必选|应用程序二进制接口对象|

**返回值 或 回调**

`Object` - 一个合约对象

##### 示例


````js
const abi=[
    {
        "name": "initWallet",
        "inputs": [
            {
                "name": "owner",
                "type": "string"
            },{
                "name": "required",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "submitTransaction",
        "inputs": [
            {
                "name": "destination",
                "type": "string"
            },{
                "name": "from",
                "type": "string"
            },{
                "name": "value",
                "type": "uint64"
            },{
                "name": "data",
                "type": "string"
            },{
                "name": "len",
                "type": "uint64"
            },{
                "name": "time",
                "type": "uint64"
            },{
                "name": "fee",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "false",
        "type": "function"
    },{
        "name": "confirmTransaction",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "revokeConfirmation",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "executeTransaction",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "isConfirmed",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "int32"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getRequired",
        "inputs": [],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getConfirmationCount",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getTransactionCount",
        "inputs": [
            {
                "name": "pending",
                "type": "int32"
            },{
                "name": "executed",
                "type": "int32"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getTransactionList",
        "inputs": [
            {
                "name": "from",
                "type": "uint64"
            },{
                "name": "to",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getOwners",
        "inputs": [],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getConfirmations",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },
    {
        "name": "getTransactionIds",
        "inputs": [
            {
                "name": "from",
                "type": "uint64"
            },{
                "name": "to",
                "type": "uint64"
            },{
                "name": "pending",
                "type": "int32"
            },{
                "name": "executed",
                "type": "int32"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    }
]

const MyContract = web3.eth.contract(abi);

const myContractInstance = MyContract.at('0x91b0ac240b62de2f0152cac322c6c5eafe730a84');


````


````js
var MyContract = web3.eth.contract(abi);

// instantiate by address
var contractInstance = MyContract.at([address]);

// deploy new contract
var contractInstance = MyContract.new([contructorParam1] [, contructorParam2], {data: '0x12345...', from: myAccount, gas: 1000000});

// Get the data to deploy the contract manually
var contractData = MyContract.new.getData([contructorParam1] [, contructorParam2], {data: '0x12345...'});
// contractData = '0x12345643213456000000000023434234'


````

#### contract.getPlatONData

##### 接口声明

    MyContract.myMethod.getPlatONData(param1 [, param2, ...])

##### 参数

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|param1/param2/...|String/Number|必选|零或多个函数参数|

**返回值 或 回调**

`Object` - 一个合约对象

##### 示例

#### contract.decodePlatONCall

> 根据abi中fnName下的outputs类型解析call返回结果

##### 接口声明

    MyContract.decodePlatONCall( callResult, [fnName])

##### 参数

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|callResult|String|必选|应用程序二进制接口对象|
|fnName|String|可选|不传，callResult将string解析callResult,如果传了，会找到abi中name等于fnName下的outputs，按照outputs.type解析|

**返回值 或 回调**

`code` - 0表示成功
`data` - 解析出来的数据

##### 示例


````js
    MyContract.decodePlatONCall( '0x',)


````

#### contract.decodePlatONLog

> 解析交易回执中logs的某一项

##### 接口声明

    MyContract.decodePlatONLog(log)

##### 参数

| 名称 |类型|属性|含义|
| :------: |:------: |:------: | :------: |
|log||Object|交易回执中logs的某一项|

**返回值 或 回调**

`Array` - 解析后的日志数组

##### 示例


````js
const data=web3.eth.getTransactionReceipt('0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f');

let res = myContractInstance.decodePlatONLog(data.logs[0]);


````
