<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Contents**

- [简介](#%E7%AE%80%E4%BB%8B)
- [使用说明](#%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)
- [常见问题及注意事项](#%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
  - [注意事项](#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
  - [基础问题](#%E5%9F%BA%E7%A1%80%E9%97%AE%E9%A2%98)
  - [更多问题](#%E6%9B%B4%E5%A4%9A%E9%97%AE%E9%A2%98)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


## 简介 

`ctool`：一个可用于快速进行合约发布、交易发送（合约调用）、查询的简易工具集。

* [window版本](https://download.platon.network/ctool-windows-amd64.exe)

> 暂时仅提供`windows`使用版本，更多版本持续开发中。

## 使用说明

```
$ ctool.exe <command> [--addr contractAddress] [--type txType(default:2)] [--func funcInfo] --abi <abi_path> --code <wasm_path> [--config <config_path>]
```

* `command` 待执行的命令,主要有：deploy(发布合约)、invoke(合约调用)、getTxReceipt(查询回执)；
* `abi_path` ABI文件的路径，示例中为：`firstdemo.cpp.abi.json`；
* `wasm_path` WASM文件路径，示例中为：`firstdemo.wasm`；
* `config_path` 配置文件路径，用于配置节点及账户信息；

**Command Options**

* `--addr`：调用合约时使用，用于指明需要调用的合约地址；
* `--type`：调用合约时指定当次的交易类型，如果未指定则默认为`2`。可取值为：`2`(普通交易)、`5`(MPC交易)
* `--func`：调用合约时用于指定调用的方法名，如：--func 'sayHello("你好")'

提示：如果命令行中未明确指明配置文件路径，则会在当前工作路径读取文件：`config.json`。 

**配置文件**

范例：
```JSON
{
  "url":"http://127.0.0.1:8545",
  "from":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219"
} 
```

- `url` PlatON开放的`JSON-RPC`地址信息； 
- `from` 交易发送者的地址，注意，需要节点保证了该账户已进行了解锁动作；

**发布合约**

此步用于演示如何进行合约发布，合约发布需要两个文件，一个为后缀为`.wasm`(合约二进制)文件，
另一个为后缀`.json`(合约接口描述)文件。如何获取这两个文件请参考：[WASM合约开发指南](https://github.com/PlatONnetwork/wiki/wiki/%5BChinese-Simplified%5D-Wasm%E5%90%88%E7%BA%A6%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97)

```shell
$ ctool.exe deploy --abi ./demo.cpp.abi.json --code ./demo.wasm --config ./config.json 
```

```
trasaction hash: 0xdb0f9a28fcd447702e8d5961f47144d1ea830979e3c984acc8f72c0dca8bdcfc
contract address: 0x43355c787c50b647c425f594b441d4bd751951c1
```

命令执行后，会返回交易哈希与合约地址。

**发送交易**

合约发布成功后，接下来进行合约调用，假定测试合约中包含了方法`sayHello(string _word);`,现在对
该方法进行调用：

```shell 
$ ctool.exe invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'sayHello("HelloWorld")' --abi ./demo.cpp.abi.json --config ./config.json
```

**查询结果**

继续假设，在上一步我们执行了合约方法`sayHello`,下一步我们将调用该函数存储的值进行读取出来，这种动作
我们成为查询（call）调用。假定测试合约用包含方法：`char * getWorld();`，调用如下：

```shell 
$ ctool.exe invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'getWorld()' --abi ./demo.cpp.abi.json --config ./config.json
```

> 预期结果获取到屏幕输出的结果应该为：`HelloWorld`。

**查询交易回执**

有时候可能需要对交易发送后的回执信息进行查看，则：

```shell 
$ ctool.exe getTxReceipt 0x0b8996dadd6fd821f055affd1f95dbdf718d288a17e4ac5ed4133f3393bca44d(交易哈希)
```


## 常见问题及注意事项

### 注意事项

1.使用命令工具前请确保您使用的节点正常可用；

2.一般比较明显的错误在执行工具后会在控制台进行输出；

3.使用工具进行交易发送要求当前使用的账号在对应链接的节点已进行解锁操作；

### 基础问题

1.合约无法发布问题？

**A**: 请确保配置文件中的能量设置合理，使用的账户已在节点侧解锁。


### 更多问题

如果你有其他问题，或者你的问题在这里找不到答案，请提交一个 [issue](https://github.com/PlatONnetwork/PlatON-Go/issues/new) 。














