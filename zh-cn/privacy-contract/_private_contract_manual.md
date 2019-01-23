- [隐私合约开发指南](#隐私合约开发指南)
    - [隐私合约](#隐私合约)
    - [依赖组件](#依赖组件)
    - [隐私计算基础类型](#隐私计算基础类型)
    - [隐私计算对象](#隐私计算对象)
    - [隐私计算函数](#隐私计算函数)
    - [隐私合约开发流程](#隐私合约开发流程)
    - [百万富翁问题](#百万富翁问题)
    - [更多范例](#更多范例)

# 隐私合约使用手册

本文档提供隐私合约的编写规范，包括基础数据结构、函数定义、定制算法、编译等。

## 隐私合约

隐私合约是元智能合约Sophia的一种(注：参见白皮书)，使用C++语言进行开发，运行在PlatON公链节点的隐私合约虚机（MPC VM）里。隐私合约借助安全多方计算（MPC）的能力，实现了对计算方数据的隐私保护并提供安全的多方计算的服务。

PlatON公链封装了隐私计算运算需要的基础数据类型，并提供这些数据类型的安全运算，用户可以使用这些数据类型，自由组合实现自己的隐私算法。PlatON公链设计并提供了隐私计算安全协议来传输数据和计算结果，参与方计算方在不泄露实际数据前提下，实现安全计算。

当前版本仅提供两方安全计算的隐私合约编写的规范，三方及多方隐私计算计算版本将陆续推出。

## 依赖组件

- Google Protobuf

  Google Protobuf即[protobuf]，可以将`.proto`文件编译成对应编/解码头文件和源文件， 提供pb消息打包和解包能力。

- clang编译器

  [clang]编译器负责对c++程序编译。隐私合约使用c++编写，需要编译生成IR文件（编译中间文件）或字节码文件提供给`JIT虚拟机`执行。

- plang 编译器

  [plang]是PlatON定制的编译器前端，它把隐私合约编译后输出算法IR(或字节码）、wasm智能合约、JAVA代理程序。

  plang编译器运行时依赖于clang，它需要与clang安装到同一个目录，一般将plang拷贝到clang所在的目录即可。

- PlatON 隐私计算组件库

  当前使用[emp-tool]库，提供隐私计算相关的头文件和MPC计算对象。

##  隐私计算基础类型

  隐私合约支持8位、16位、32位、64位整型以及其他任意长度的整型计算，统一使用**隐私计算**运算类型（**emp::Integer**）来表示隐私数据，借助MPC计算协议进行多方安全计算。
   计算双方的角色要分别为Alice、Bob，支持的隐私计算的算术符包括：**+, -, *, /, |, &, ^, %，移位** 等。


下面以32位整型的隐私加法运算为例：

1. 声明32位有符号整型``隐私计算变量``


```cpp
emp::Integer v1(123, emp::ALICE);//声明Alice 隐私计算变量
emp::Integer v2(456, emp::BOB);//声明Bob 隐私计算变量


```

2.  隐私加法运算


```cpp
emp::Integer result = v1 + v2; //进行隐私加法运算


```

3.  解密获取结果值


```cpp
int plain_result = result.reveal();//Alice和Bob都获得计算结果579


```
***注意*** :
    1. 隐私计算运算类型变量运算要求Alice和Bob在同类型、同长度的条件下进行，否则计算报错。
    2. 隐私计算运算类型变量运算要求计算双方，一个为Alice角色，另一个为Bob角色，否则计算无法进行。


## 隐私计算对象

当前隐私计算对象仅仅支持整型运算，即**emp::Integer**，作为隐私计算的实现对象，提供算术运算、绝对值、位运算等基础隐私计算。

### 接口


```cpp
namespace emp 
{
    class Integer : public Swappable<Integer>, public Comparable<Integer>
    {
	Integer(Integer&& in);
	Integer(const Integer& in);
	Integer& operator= (Integer rhs);

	Integer(int len, const void * b);
	~Integer();

	Integer(const int8_t& value, int party = PUBLIC);
	Integer(const int16_t& value, int party = PUBLIC);
	Integer(const int32_t& value, int party = PUBLIC);
	Integer(const int64_t& value, int party = PUBLIC);

	Integer(int length, const string& str, int party = PUBLIC);
	Integer(int length, long long input, int party = PUBLIC);
	Integer() :length(0),bits(nullptr){}

    //Comparable
	Bit geq(const Integer & rhs) const;
	Bit equal(const Integer & rhs) const;

    //Swappable
	Integer select(const Bit & sel, const Integer & rhs) const;
	Integer operator^(const Integer& rhs) const;
	
	string reveal_string(int party = PUBLIC) const;
	int64_t reveal(int party = PUBLIC) const;
	uint64_t reveal_uint(int party = PUBLIC) const;

	Integer abs() const;
	Integer& resize(int length, bool signed_extend = true);
	Integer modExp(Integer p, Integer q);
	Integer leading_zeros() const;
	Integer hamming_weight() const;

	Integer operator<<(int shamt)const;
	Integer operator>>(int shamt)const;
	Integer operator<<(const Integer& shamt)const;
	Integer operator>>(const Integer& shamt)const;

	Integer operator+(const Integer& rhs)const;
	Integer operator-(const Integer& rhs)const;
	Integer operator-()const;
	Integer operator*(const Integer& rhs)const;
	Integer operator/(const Integer& rhs)const;
	Integer operator%(const Integer& rhs)const;
	Integer operator&(const Integer& rhs)const;
	Integer operator|(const Integer& rhs)const;

	int size() const;
	Bit& operator[](int index);
	const Bit & operator[](int index) const;
    };
}//emp


```

## 隐私计算函数

**隐私计算函数定义**：

  能够参与隐私计算的函数，输入参数为2个以上，参数1对应为Alice，参数2对应为Bob，其他参数为附加参数。

**隐私计算函数定义原型**：


```cpp
return_type function_name(declare_type in1, declare_type in2, ...)


```
**含义**：


```
return_type:   返回类型
declare_type: C++基本类型或Google Protobuf定义的类型
in1: Alice的输入
in2: Bob的输入


```
**注意**：
1. 隐私计算函数声明的输入参数类型和返回类型要求:
	- 基本类型: char, unsigned, int, short, long, char, float, double等 ;
	- 字符串： std::string ;
	- 用户自定义类型： [protobuf]定义的消息类型;
2. 隐私计算函数类型编码：
	- 返回值全部使用[protobuf]编码，包括基本类型，string，用户自定义类型
	- 输入参数全部使用使用[protobuf]编码，包括基本类型、string和用户自定义类型  
3. 使用MPC Java Sdk的数据服务程序，已经完成了基本数据类型的[protobuf]打包和解包处理

-------------------

## 隐私函数范例说明

下面定义一个隐私计算函数实例，并使用用户自定义类型作为参数。

1. 定义用户自定类型Foo（使用Google Protobuf定义）


```protobuf
syntax = "proto3";
message Foo {
    int32 item1 = 1;
    int32 item2 = 2;
    string info = 3;
};


```

2. 定义函数

	这里定义函数实现进行了取绝对值运算，Alice和Bob的输入值在隐私运算过程都不会泄露，最终获得计算更新后的Foo对象。

	代码如下：


```cpp
/**
* 对Alice输入的Foo对象字段item1、item2分别和Bob的输入值in2进行绝对值运算，返回计算后的Foo对象
*/
Foo foo_abs(const Foo& in1, int32_t in2, const std::string& extra)
{
    Foo foo = in1;
    emp::Integer item1(in1.item1(), emp::ALICE);//Alice输入item1
    emp::Integer item2(in1.item2(), emp::ALICE);//Alice输入item2
    emp::Integer v2(in2, emp::BOB);//Bob输入
    emp::Integer ret1 = (item1 - v2).abs();//隐私计算绝对值运算
    emp::Integer ret2 = (item2 - v2).abs();//隐私计算绝对值运算
    foo.set_item1(ret1.reveal());//更新item1
    foo.set_item2(ret2.reveal());//更新item2
    foo.set_info(extra);

    return foo;
}


```

3. 调用隐私运算说明
例如，Alice和Bob分别输入：{1000, 100}， 900进行foo_abs 隐私计算

Alice调用隐私运算代码如下：


```cpp
 /**
 外部构建Alice对象
 alice.set_item1(1000);
 alice.set_item2(100);
 */
 Foo alice;
 int in2 = 0;//Bob输入对本地无意义
 Foo result = foo_abs(alice, in2, "abs");//参数1的值只有Alice知道，参数2对于Alice无意义，依赖隐私计算对端的Bob


```

Bob调用隐私运算如下：


```cpp

//Alice输入参数对本地Bob无意义
Foo alice;
int in2 = 900;//外部构建Bob输入
Foo result = foo_abs(alice, in2, "abs");//参数2的值只有Bob知道，参数1对于Bob无意义，依赖隐私计算对端的Alice，


```
可以发现Alice和Bob调用同样的代码：``Foo result = foo_abs(alice, in2)``， 仅仅输入不一样，并且都只使用自己的输入参与两方计算，另外一个无关输入可以任意（这里默认为0）。


## 隐私合约开发流程

编写一个带自定义输入的隐私合约实例，并详细介绍隐私算法的编写细节，编译参数使用等。

### 隐私算法编写

隐私算法定义包含两部分：输入类型定义和算法实现。
- 类型定义：输入类型使用内置类型和自定义类型（均为Google Protobuf编码）
- 算法实现：自己定义实现的隐私计算函数，使用隐私计算对象作为MPC计算承载体

- **protobuf定义**：


```protobuf
syntax = "proto3";
message Foo {
    int32 item1 = 1;
    int32 item2 = 2;
    string info = 3;
};


```
保存protobuf定义为msg.proto文件，编译`.proto`文件:


```bash
# path/to/protobuf/include为protobuf头文件路径
> protoc msg.proto --cpp_out=./ -Ipath/to/protobuf/include --java_out=./


```
生成msg.pb.h、msg.pb.cc，Msg.java。

- **隐私合约实现**:

编写隐私合约，包含pb生成的文件和integer.h头文件，可以使用任意文本编辑器编写代码。
下面编写一个输入Foo对象中两个整数item1，item2和另一个输入整型取绝对值，得到一个新的Foo对象。

代码如下：


```cpp

    #include "msg.pb.h"
    #include "integer.h"

    /**
    * 对Alice输入的Foo对象字段item1、item2分别和Bob的输入值进行绝对值运算，返回计算后的Foo对象
    */
    Foo foo_abs(const Foo& in1, int32_t in2, const std::string& extra)
    {
        Foo foo = in1;
        emp::Integer item1(in1.item1(), emp::ALICE);//Alice输入item1
        emp::Integer item2(in1.item2(), emp::ALICE);//Alice输入item2
        emp::Integer v2(in2, emp::BOB);//Bob输入
        emp::Integer ret1 = (item1 - v2).abs();//隐私计算绝对值运算
        emp::Integer ret2 = (item2 - v2).abs();//隐私计算绝对值运算
        foo.set_item1(ret1.reveal());//更新item1
        foo.set_item2(ret2.reveal());//更新item2
        foo.set_infO(extra);

        return foo;
    }


```
保存为foo_sample.cpp


- **编译**

使用[plang]编译foo_sample.cpp。

配置命令参数文件：


```bash
config.json配置为：
{
     "invitor":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219",
     "parties":["0x60ceca9c1290ee56b98d4e160ef0453f7c40d219", "0x3771c08952f96e70af27324de11bb380ec388ec3"],
     "method-price":{"foo_abs":200},
     "profit-rules":{"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":1, "0x3771c08952f96e70af27324de11bb380ec388ec3":2},
     "urls":{"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":"DirectNodeServer:default -h 10.10.8.155 -p 10001",
             "0x3771c08952f96e70af27324de11bb380ec388ec3":"DirectNodeServer:default -h 10.10.8.155 -p 10002"}
}


```

命令：


```bash
./plang foo_sample.cpp -config config.json -mpcc foo_contract.cpp -java-code ./java -protobuf-cc msg.pb.cc -protobuf-java Msg.java


```
输出：


```
编译中间文件字： foo_sample.cpp.bc
隐私合约(wasm)： foo_contract.cpp
JAVA数据接入代理代码： 当前java目录生产java sdk代理代码


```
**[plang]**使用可以参考[plang编译器使用手册]。

### 百万富翁问题
[百万富翁问题] 是姚期智于1982年提出的一个经典案例，即两位百万富翁期望不泄露自己财产隐私的条件下，知道对方是否比自己更有钱。使用基于[Platon]的隐私合约可以很容易解决百万富翁问题。如下：


```cpp
#include "integer.h"
/**
* 对Alice输入的32位整型，Bob输入32位整型，Alice减去BOB
*/
int Millionaire(int32_t in1, int32_t in2, const std::string& category)
{
    emp::Integer item1(in1, emp::ALICE);//Alice输入item1
    emp::Integer item2(in2, emp::BOB);//Bob输入item2
    emp::Integer ret = (item1 - item2);//compare
    return ret.reveal() > 0 ? 1 : 0;//get the result
}


```
###  更多范例
更多范例及细节可以参考[privacy contract sdk](https://github.com/PlatONnetwork/wiki/wiki/%5BChinese-Simplified%5D%E9%9A%90%E7%A7%81%E5%90%88%E7%BA%A6SDK%E6%8C%87%E5%8D%97)

---------------------
[Google Protobuf]:https://developers.google.com/protocol-buffers/
[protobuf]:https://developers.google.com/protocol-buffers/
[PlatON]:https://github.com/PlatONnetwork/wiki/wiki
[plang]:https://github.com/PlatONnetwork/private-contract-compiler/
[百万富翁问题]:https://en.wikipedia.org/wiki/Yao%27s_Millionaires%27_Problem
[web3j]:https://docs.web3j.io/
[plang编译器使用手册]:https://github.com/PlatONnetwork/wiki/wiki/[Chinese-Simplified]-plang%e7%bc%96%e8%af%91%e5%99%a8%e4%bd%bf%e7%94%a8%e6%89%8b%e5%86%8c
[privacy contract sdk]:https://github.com/PlatONnetwork/wiki/wiki/%5BChinese-Simplified%5D%E9%9A%90%E7%A7%81%E5%90%88%E7%BA%A6SDK%E6%8C%87%E5%8D%97