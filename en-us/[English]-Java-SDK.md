-   [Overview](#Overview)
-   [Release notes](#Release-notes)
    -   [v0.2.0 Release notes](#v0.2.0-Release-notes)
-   [Quick start](#Quick-start)
    -   [Installation instruction](#Installation-instruction)
    -   [Code initialization](#Code-initialization)
-   [Contract](#Contract)
    -   [Contract Sample Code](#Contract-Sample-Code)
    -   [Contract Java wrapper building](#Contract-Java-wrapper-building)
    -   [Load Contract](#Load-Contract)
    -   [Deploy Contract](#Deploy-Contract)
    -   [Contract function call](#Contract-function-call)
    -   [Contract sendRawTransaction function](#Contract-sendrawtransaction-function)
    -   [Contract event](#Contract-event)
-   [web3](#web3)
    -   [web3 eth related (standard JSON RPC )](#web3-eth-related-standard-json-rpc)
    -   [New interfaces](#New-interfaces) 
        -   [ethPendingTx](#ethpendingtx)
	
# Overview
> Java SDK is a Java development kit provided by PlatON for Java developers working on PlatON public chain.

# Release notes

## v0.2.0 Release notes

1. Support PlatON smart contracts

# Quick start

## Installation instruction

### Environment requirement

1. jdk1.8

### maven
1. SDK lib address https://sdk.platon.network/nexus/content/groups/public/
2. Build by maven configuration
```
<dependency>
    <groupId>com.platon.client</groupId>
    <artifactId>core</artifactId>
    <version>x.x.x</version>
</dependency>
```
3. Build by gradle
```
compile "com.platon.client:core:x.x.x"
```
### Building contract Java wrapper
1. Download SDK package from url: https://download.platon.network/client-sdk.zip
2. Folder structures after uncompress
```
.
+-- _bin
|   +-- client-sdk.bat                 //windows executable
|   +-- client-sdk                     //linux executable
+-- _lib
|   +-- xxx.jar                        //JAR file
|   +-- ...
```
3. Execute coresponding ./client-sdk command in bin folder above  

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

## Code initialization
```
Web3j web3 = Web3j.build(new HttpService("http://localhost:6789"));
```

# Contract

## Contract Sample Code

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

## Build contract Java wrapper
1. How to code wasm contract, ABI(wasm file) and BIN(json file), please refer to [wiki](https://github.com/PlatONnetwork/wiki/wiki)
2. Use contract Java wrapper building toolkit
```
client-sdk wasm generate /path/to/token.wasm /path/to/token.cpp.abi.json -o /path/to/src/main/java -p com.your.organisation.name
```

## Load Contract
```
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Token contract = Token.load(binData, "0x<address>", web3j, credentials, new StaticGasProvider(GAS_PRICE, GAS_LIMIT));
```

## Deploy Contract
```
Web3j web3 = Web3j.build(new HttpService("http://localhost:6789"));
Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Token contract = Token.deploy(web3, credentials, binData, new StaticGasProvider(GAS_PRICE,GAS_LIMIT)).send();
```

## Contract ethCall call
```
Request<?, org.web3j.protocol.core.methods.response.EthCall> req = web3j.ethCall(Transaction.createEthCallTransaction(
               "0xa70e8dd61c5d32be8058bb8eb970870f07233155",
               "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
                       "0x0"),
               DefaultBlockParameter.valueOf("latest"));
org.web3j.protocol.core.methods.response.EthCall res = req.send();
String value = res.getValue();

```

## Contract sendRawTransaction call
```
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));

String signedData = "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675";
Request<?, org.web3j.protocol.core.methods.response.EthSendTransaction> req = web3j.ethSendRawTransaction(signedData);
org.web3j.protocol.core.methods.response.EthSendTransaction res = req.send();
String transactionHash = res.getTransactionHash();

```
## Contract event
Assume Contrace has an event "transfer", then we could monitor this event as below

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
## web3 eth related (standard JSON RPC)
- For detailed api usage please refer [web3j github](https://github.com/web3j/web3j)

## New interfaces
### ethPendingTx
>Returns the pending transaction list.

**Parameters**

      Name                  Type            Attributes   Description
    ----------------------- -------------- ------ -------
                         (No Parameters)

**Return value**

`Request<?, EthPendingTransactions>`
transactions data member of EthPendingTransactions object

**Sample Code**
```
Request<?, EthPendingTransactions> req = web3j.ethPendingTx();
EthPendingTransactions res = req.send();
List<Transaction> transactions = res.getTransactions();
```
