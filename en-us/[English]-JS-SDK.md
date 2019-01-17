Javascript SDK

- [Overview](#Overview)
- [Release notes](#Release-notes)
  - [v0.2.0 Release notes](#v020-Release-notes)
- [Quick start](#Quick-start)
  - [Installation instruction](#Installation-instruction)
  - [Code initialization](#Code-initialization)
- [Contract](#Contract)
  - [Sample contract](#Sample-contract)
  - [Deploy Contract](#Deploy-Contract)
  - [Contract function call](#Contract-function-call)
  - [Contract sendRawTransaction function](#Contract-sendrawtransaction-function)
- [web3](#web3)
  - [web3 eth related (standard JSON RPC)](#web3-eth-related-standard-json-rpc)
  - [New interfaces](#New-interfaces)
    - [contract](#contract)
      - [getPlatONData](#contract.getPlatONData)
      - [decodePlatONCall](#contract.decodePlatONCall)
      - [decodePlatONLog](#contract.decodePlatONLog)

## Overview
> Javascript SDK is a JS development kit provided for JS developers working on PlatON

## Release notes

### v0.2.0 Release notes

1. Support PlatON wasm contract

### Quick start

### Installation instruction

Installation using node.js:

`cnpm i https://github.com/PlatONnetwork/client-sdk-js`

### Code initialization

Next step is to create a web3 instance with provider. To avoid overwrite your existing provider, better to check if web3 instance already exists. For example, when use Mist.


```js
if (typeof web3 !== 'undefined') {
    web3 = new Web3(web3.currentProvider);
} else {
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));
}


```

### Contract

How to code wasm contract, ABI(wasm file) and BIN(json file), please refer to [wiki](https://github.com/PlatONnetwork/wiki/wiki)

#### Sample contract


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

#### Deploy Contract

##### Interface declaration

    MyContract.deploy(deployData [, callback])

##### Parameters

|  Name   |    Type  |       Attributes  | Description |
|:-------- |:-------- |:-------- | :-------- |
|deployData |String |Mandatory | Signed transaction data in hex |
|callback |Funciton |Optional | call back function for asynchronous execution |

##### Sample code


````js
const Web3 = require('web3'),
    fs = require('fs'),
    path = require('path'),
    Tx = require('ethereumjs-tx'),
    abi = require('./multisig.cpp.abi.json') // binary interface of application in abi format
    ;

const provider = 'http://localhost:6789',
    privateKey='fd098d39e56115762cf28d7d223a4303a42a42451da6e7da37cb40a81a246d98',//Private key
    address='0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b'

const web3 = new Web3(new Web3.providers.HttpProvider(provider))

const MyContract  = web3.eth.contract(abi)

const source = fs.readFileSync(path.join(__dirname, './demo.wasm'));//WebAssembly binary bin file

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
            console.log("contract deploy transaction hash: " + myContract.transactionHash) // transaction hash
        } else {
            // Successful deployment
            console.log("contract deploy transaction address: " + myContract.address)// contract address deployed
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

#### Contract function call

> Call contract function, return a value

##### Interface

    web3.eth.call(Object [, defaultBlock] [, callback])

##### Parameters

Name                  Type         Attributes   Description
----------------------- ------------ ------------ -------
|Object |String |Mandatory| Return an object of transaction, different from `web3.eth.sendTransaction` where `from` Attributes is optional|
|defaultBlock|Number/String |Optional| use `web3.eth.defaultBlock` when empty|
|callback|Funciton  |Optional|Callback function for asynchronous execution|

##### Sample code


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

#### Call contract sendRawTransaction

> Send transaction signed by private key

##### Interface

    web3.eth.sendRawTransaction(signedTransactionData [, callback])

##### Parameters

| Name |Type|Attributes|Description|
| :------: |:------: |:------: | :------: |
|signedTransactionData |String |Mandatory|Signed transaction data in hex|
|callback|Funciton  |Optional|Callback function for asynchronous execution|

##### Sample code


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

### web3 eth related (standard JSON RPC)
- For detailed api usage please refer [web3j github](https://github.com/ethereum/wiki/wiki/JavaScript-API)

### New interfaces

#### contract

##### Interface

    web3.eth.contract(abi)

##### Parameters

| Name |Type|Attributes|Description|
| :------: |:------: |:------: | :------: |
|abi|Array|Mandatory|Binary interface of application like ABI file|

**Return value or callback**

`Object` - a contract object

##### Sample code


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

##### Interface

    MyContract.myMethod.getPlatONData(param1 [, param2, ...])

##### Parameters

| Name |Type|Attributes|Description|
| :------: |:------: |:------: | :------: |
|param1/param2/...|String/Number|Mandatory|zero or more parameters|

**Return value or callback**

`Object` - one contract object

##### Sample code

#### contract.decodePlatONCall

> Parse call result based on ABI file details, like outputType defined by fnName in ABI

##### Interface

    MyContract.decodePlatONCall( callResult, [fnName])

##### Parameters

| Name |Type|Attributes|Description|
| :------: |:------: |:------: | :------: |
|callResult|String|Mandatory|Binary interface of application, like ABI file|
|fnName|String|Optional| interpret callResult as string when missing, otherwise, will parse based on details defined in abi file|

**Return value or callback**

`code` - 0 Success
`data` - Parsed data

##### Sample code


````js
    MyContract.decodePlatONCall( '0x',)


````

#### contract.decodePlatONLog

> Parsing a log from transaction receipt logs

##### Interface

    MyContract.decodePlatONLog(log)

##### Parameters

| Name |Type|Attributes|Description|
| :------: |:------: |:------: | :------: |
|log||Object| a log from transaction receipt logs|

**Return value or callback**

`Array` - Parsed array of logs

##### Sample code


````js
const data=web3.eth.getTransactionReceipt('0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f');

let res = myContractInstance.decodePlatONLog(data.logs[0]);


````
