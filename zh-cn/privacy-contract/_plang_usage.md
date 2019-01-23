# plang编译器使用说明

## 简述
plang编译器基于llvm和clang编译器，支持clang/gcc编译器的所有选项，并加入了一些合约选项。另外plang的使用依赖于clang，因此需要clang和plang处于同一安装目录

## 用法


```bash
    plang [clang args] [options] <inputs> <config-file>

    clang args: clang/gcc支持的选项
    options: plang的合约选项
    inputs: 输入文件（可以输入多个文件，会被链接起来）
    config-file:配置文件


```


## 合约编译选项


```bash
    --help                打印选项
    -java-code <directoy> 设置java代码输出目录
    -mpcc <file>          设置合约文件输出目录
    -o <file>             设置算法文件输出目录
    -S                    算法文件输出为文本


```

## 配置文件字段


```bash

    invitor      发起方地址
    parties      参与方地址列表
    method-price 每个合约函数的价格
    profit-rules 利润分配比例
    urls         参与方端口号


```

## 范例

编写百万富翁问题范例如下：


```cpp
#include "platon_integer.h"
/** 
* 对Alice输入的32位整型，Bob输入32位整型，Alice减去BOB
*/
int Millionaire(int32_t in1, int32_t in2, const std::string& category)
{
    platon::mpc::Integer item1(in1, ALICE);//Alice输入item1
    platon::mpc::Integer item2(in2, BOB);//Bob输入item2
    platon::mpc::Integer ret = (item1 - item2);//compare
    return ret.reveal_int() > 0 ? 1 : 0;//get the result
}


```
保存为test.cpp

配置：


```bash
{
"invitor":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219",
"parties":["0x60ceca9c1290ee56b98d4e160ef0453f7c40d219", "0x3771c08952f96e70af27324de11bb380ec388ec3"],
"method-price":{"foo_abs":200},
"profit-rules":{"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":1, "0x3771c08952f96e70af27324de11bb380ec388ec3":2},
"urls":
{
"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":"DirectNodeServer:default -h 10.10.8.155 -p 10001", 
"0x3771c08952f96e70af27324de11bb380ec388ec3":"DirectNodeServer:default -h 10.10.8.155 -p 10002"
}
}


```
编译：


```bash
./plang test.cpp -config config.json -mpcc millionaire_contract.wasm -java-code ./java -I./


```

