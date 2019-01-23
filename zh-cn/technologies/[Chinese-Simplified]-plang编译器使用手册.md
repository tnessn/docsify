# plang编译器使用手册
--------
- [隐私计算合约指南](#隐私计算合约指南)
    - [简述](#简述)
    - [安装](#安装)
    - [用法](#用法)
    - [合约编译选项](#合约编译选项)
    - [范例](#范例)

------------------

## 简述
plang编译器基于llvm和clang编译器，支持clang/gcc编译器的所有选项，并加入了一些合约选项。另外plang的使用依赖于一些头文件，因此需要使clang和plang处于同一安装目录

## 安装
* 安装rapidjson

```bash
    sudo apt-get update
    sudo apt-get install rapidjson-dev
```

* 编译plang

```bash 
    mkdir build   
    cd build
    cmake ..
    make
```
注意: 由于plang的编译需要较多内存，因此建议在cmake构建工程时开启以下选项：

```bash 
    -DLLVM_USE_LINKER=gold       
    -DBUILD_SHARED_LIBS=ON       
    -DCMAKE_BUILD_TYPE=Release
```

编译成功将会在build/bin生成plang可执行文件

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
    -config <file>        设置配置文件路径
    --help                打印选项
    -java-code <directoy> 设置java代码输出目录
    -mpcc <file>          设置合约文件输出路径
    -o <file>             设置算法文件输出路径
    -protobuf-cc <file>   设置protobuf生成的*.cc文件路径
    -protobuf-java <file> 设置protobuf生成的*.java文件路径
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

下面编写一个输入Foo对象中两个整数item1，item2和另一个输入整型取绝对值，得到一个新的Foo对象。

- **protobuf定义**：

```protobuf
syntax = "proto3";
message Foo {
    int32 item1 = 1;
    int32 item2 = 2;
    string info = 3;
}
```

保存protobuf定义为msg.proto文件，编译`.proto`文件,生成msg.pb.cc、msg.pb.h和Msg.java文件:

```shell
> protoc msg.proto --cpp_out=./ --java_out=./
```

- **隐私合约源码**:

编写隐私合约

```cpp
#include "msg.pb.h"
#include "integer.h"

/** 
* 对Alice输入的Foo对象字段item1，item2分别和Bob输入值进行绝对值运算，返回计算后的Foo对象
*/
Foo foo_abs(const Foo& in1, int32_t in2, const std::string& extra)
{
   Foo foo = in1;
   emp::Integer item1(in1.item1(), emp::ALICE);//Alice输入item1
   emp::Integer item2(in1.item2(), emp::ALICE);//Alice输入item2
   emp::Integer v2(in2, emp::BOB);//Bob输入
   emp::Integer ret1 = (item1 - v2).abs();//隐私计算绝对值运算
   emp::Integer ret2 = (item2 - v2).abs();//隐私计算绝对值运算
   foo.set_item1(ret1.reveal_int());//更新item1
   foo.set_item2(ret2.reveal_int());//更新item2
   foo.set_info(extra);

   return foo;
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
./plang test.cpp -config config.json -mpcc millionaire_contract.cpp -java-code ./java -protobuf-cc msg.pb.cc -protobuf-java Msg.java
```

