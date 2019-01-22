<!-- TOC -->

- [MPC编程指南](#mpc编程指南)
    - [隐私合约](#隐私合约)
        - [隐私计算基础类型](#隐私计算基础类型)
        - [隐私计算对象](#隐私计算对象)
            - [接口](#接口)
        - [隐私计算函数](#隐私计算函数)
        - [隐私计算函数使用范例](#隐私计算函数使用范例)
        - [隐私合约自定义类型范例](#隐私合约自定义类型范例)
            - [protobuf定义](#protobuf定义)
            - [隐私合约实现](#隐私合约实现)
            - [编译](#编译)
    - [隐私数据服务SDK](#隐私数据服务sdk)
        - [MPC回调接口](#mpc回调接口)
        - [接口定制说明](#接口定制说明)
        - [更多示例](#更多示例)
    - [隐私客户端SDK](#隐私客户端sdk)
        - [发起计算](#发起计算)
        - [获取任务ID](#获取任务id)
        - [获取交易回执](#获取交易回执)
        - [获取结果密文](#获取结果密文)
        - [获取结果明文](#获取结果明文)
        - [其他](#其他)

<!-- /TOC -->
----------------
# MPC编程指南

## 隐私合约

**隐私合约**是 [PlatON]([Chinese-Simplified]+%e5%bf%ab%e9%80%9f%e6%8c%87%e5%8d%97) 上一种特殊的 MPC 合约，使用`C++`语言进行开发，运行在 PlatON 节点的MPC计算虚拟机（MPC VM）里。隐私合约借助 PlatON 平台安全多方计算（MPC）的能力，实现了对计算方数据的隐私保护，提供了安全多方计算（MPC）服务。

PlatON底层基础设施封装了隐私计算运算需要的基础数据类型，并提供这些数据类型的安全运算，用户可以使用这些数据类型，自由组合实现自己的隐私算法。PlatON底层设计并提供了隐私计算安全协议来传输数据和计算结果，参与计算方在不泄露实际数据前提下，实现隐私计算。

当前版本仅提供两方安全计算的隐私合约编写的规范，三方及多方隐私计算计算版本将陆续推出。

###  隐私计算基础类型

隐私合约支持8位、16位、32位、64位整型以及其他任意长度的整型计算，统一使用**隐私计算**运算类型**emp::Integer**来表示隐私数据，借助MPC计算协议进行多方安全计算。
计算双方的角色要分别为Alice、Bob，支持的隐私计算的算术运算符包括：**+, -, *, /, |, &, ^, %，移位** 等。


下面以32位整型的隐私加法运算为例：

1. 声明32位有符号整型**隐私计算变量**


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
​    1. 隐私计算运算类型变量运算要求Alice和Bob在同类型、同长度的条件下进行，否则计算报错。
​    2. 隐私计算运算类型变量运算要求计算双方，一个为Alice角色，另一个为Bob角色，否则计算无法进行。

### 隐私计算对象

当前隐私计算对象仅仅支持整型运算，即**emp::Integer**，作为隐私计算的实现对象。提供算术运算、绝对值、位运算等基础隐私计算。

#### 接口


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

### 隐私计算函数

**隐私计算函数定义**：

能够参与隐私计算的函数，输入参数为2个以上，参数1对应为Alice的输入，参数2对应为Bob的输入，暂时不支持更多参数。

**隐私计算函数定义原型**：


```cpp
return_type function_name(declare_type in1, declare_type in2)


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
   - 基本类型：char, unsigned, int, short, long, char, float, double等 ;
   - 字符串： std::string ;
   - 用户自定义类型：[protobuf](#依赖组件) 定义的消息类型;
2. 隐私计算函数类型编码：
   - 返回值全部使用 [protobuf](#依赖组件) 编码，包括基本类型，string，用户自定义类型
   - 输入参数全部使用使用 [protobuf](#依赖组件) 编码，包括基本类型、string和用户自定义类型  
3. 使用 [MPC Java Sdk] 的数据服务程序，已经完成了基本数据类型的 `protobuf` 打包和解包处理

### 隐私计算函数使用范例

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
Foo foo_abs(const Foo& in1, int32_t in2)
{
    Foo foo = in1;
    emp::Integer item1(in1.item1(), emp::ALICE);//Alice输入item1
    emp::Integer item2(in1.item2(), emp::ALICE);//Alice输入item2
    emp::Integer v2(in2, emp::BOB);//Bob输入
    emp::Integer ret1 = (item1 - v2).abs();//隐私计算绝对值运算
    emp::Integer ret2 = (item2 - v2).abs();//隐私计算绝对值运算
    foo.set_item1(ret1.reveal());//更新item1
    foo.set_item2(ret2.reveal());//更新item2

    return foo;
}


```

3. 调用隐私运算说明

  例如，Alice和Bob分别输入：{1000, 100}， 900进行 `foo_abs` 隐私计算

Alice调用隐私运算代码如下：


```cpp
 /**
 外部构建Alice对象
 alice.set_item1(1000);
 alice.set_item2(100);
 */
 Foo alice;
 int in2 = 0;//Bob输入对本地无意义
 Foo result = foo_abs(alice, in2);//参数1的值只有Alice知道，参数2对于Alice无意义，依赖隐私计算对端的Bob


```

Bob调用隐私运算如下：


```cpp

//Alice输入参数对本地Bob无意义
Foo alice;
int in2 = 900;//外部构建Bob输入
Foo result = foo_abs(alice, in2);//参数2的值只有Bob知道，参数1对于Bob无意义，依赖隐私计算对端的Alice，


```
可以发现Alice和Bob调用同样的代码：``Foo result = foo_abs(alice, in2)``， 仅仅输入不一样，并且都只使用自己的输入参与两方计算，另外一个无关输入可以任意（这里默认为0），结果得到相同的结果。


### 隐私合约自定义类型范例

编写一个带自定义输入的隐私合约实例，并详细介绍隐私算法的编写细节，编译参数使用等。

隐私算法定义包含两部分：输入类型定义和算法实现。
- 类型定义：输入类型使用内置类型和自定义类型（均为Google Protobuf编码）
- 算法实现：自己定义实现的隐私计算函数，使用隐私计算对象作为MPC计算承载体

#### protobuf定义


```protobuf
syntax = "proto3";
message Foo {
    int32 item1 = 1;
    int32 item2 = 2;
    string info = 3;
};


```
保存 protobuf 定义为 msg.proto 文件，编译`.proto`文件:


```bash
$ protoc msg.proto --cpp_out=./ -I{path/to/protobuf/include} --java_out=./


```
其中 *{path/to/protobuf/include}* 需替换为 protobuf 安装头文件路径。这里因为使用脚本默认安装，所以为 `/usr/local/include/google/protobuf`。

若编译过程中出现以下错误：


```bash
msg.proto: File does not reside within any path specified using --proto_path (or -I).  You must specify a --proto_path which encompasses 
this file.  Note that the proto_path must be an exact prefix of the .proto file names -- protoc is too dumb to figure out when two paths 
(e.g. absolute and relative) are equivalent (it's harder than you think).


```

请添加 `--proto_path=./` 或 `-I ./` 选项指定 proto 文件所在路径后重试。修改后命令如下：


```bash
$ protoc --proto_path=./ msg.proto --cpp_out=./ -I{path/to/protobuf/include} --java_out=./


```

若出现其他问题，请执行 `protoc --help` 后参考 **protoc** 详细用法再重试。

命令运行后生成 **msg.pb.h**、**msg.pb.cc**、**Msg.java** 三个文件。

#### 隐私合约实现

编写隐私合约可以使用任意文本编辑器编写代码。

下面编写一个输入Foo对象中两个整数item1，item2和另一个整型入参取绝对值，得到一个新的Foo对象。

代码如下：


```cpp

    #include "msg.pb.h"
    #include "integer.h"

    /**
    * 对Alice输入的Foo对象字段item1、item2分别和Bob的输入值进行绝对值运算，返回计算后的Foo对象
    */
    Foo foo_abs(const Foo& in1, int32_t in2)
    {
        Foo foo = in1;
        emp::Integer item1(in1.item1(), emp::ALICE);//Alice输入item1
        emp::Integer item2(in1.item2(), emp::ALICE);//Alice输入item2
        emp::Integer v2(in2, emp::BOB);//Bob输入
        emp::Integer ret1 = (item1 - v2).abs();//隐私计算绝对值运算
        emp::Integer ret2 = (item2 - v2).abs();//隐私计算绝对值运算
        foo.set_item1(ret1.reveal());//更新item1
        foo.set_item2(ret2.reveal());//更新item2
        
        return foo;
    }


```
保存文件为 **foo_sample.cpp**。



#### 编译

合约使用 [plang](#) 编译器编译 。更多使用方法参考[plang编译器使用手册]。

完整的隐私合约项目除了包含新编写的 `foo_sample.cpp` 文件还应包含 `protobuf` 生成的文件和 `integer.h` 头文件。

`plang` 编译器编译合约命令如下：


```bash
$ ./plang foo_sample.cpp -mpcc foo_contract.cpp -java-code ./ -config ./config.json  -protobuf-cc ./msg.pb.cc -protobuf-java ./Msg.java


```

**参数：**

- `foo_sample.cpp` ：隐私合约源代码文件
- `-mpcc`：指定输出文件名。示例中为：当前目录下 `foo_contract.cpp`
- `-java-code`：指定输出 java 代码文件地址。示例中为：当前目录（./）
- `-config`：指定配置文件。示例中为：当前目录下 `config.json`
- `-protobuf-cc`：指定 protobuf 编译出来的 cc 源文件。示例中为：当前目录下 `msg.pb.cc`
- `-protobuf-java`：指定 protobuf 编译出来的 java 源文件。示例中为：当前目录下 `Msg.java`

`config.json` **配置文件** 如下：


```bash
{
     "invitor":"0x9a568e649c3a9d43b7f565ff2c835a24934ba447",
     "parties":["0x9a568e649c3a9d43b7f565ff2c835a24934ba447", "0xce3a4aa58432065c4c5fae85106aee4aef77a115"],
     "method-price":{"foo_abs":200},
     "profit-rules":{"0x9a568e649c3a9d43b7f565ff2c835a24934ba447":1, "0xce3a4aa58432065c4c5fae85106aee4aef77a115":2},
     "urls":{"0x9a568e649c3a9d43b7f565ff2c835a24934ba447":"DirectNodeServer:default -h 127.0.0.1 -p 10001",
             "0xce3a4aa58432065c4c5fae85106aee4aef77a115":"DirectNodeServer:default -h 127.0.0.1 -p 10002"}
}


```

配置文件字段简述：

- `invitor` ：计算发起方地址，parties 之一
- `parties`：计算参与方地址列表，目前只支持两方参与
- `method-price`：为每个合约函数定价
- `profit-rules`：利润分配比例
- `urls`：计算参与方MPC服务地址信息。其值为json对象，值的key为计算参与方地址；值的value为字符串，用来设置MPC服务地址信息，其中 `DirectNodeServer` 固定，`-h` 和 `-p` 分别为节点地址和MPC服务端口。MPC端口在[这里]([Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#为节点启用MPC功能)设定。

**提示**：

当计算参与方不在一台物理机器上并且开启防火墙时，配置文件中 `urls` 指定的节点MPC服务端口若未设置对外开放将无法访问。此时，需要设置防火墙策略以允许访问，或者在测试环境下关闭防火墙。


**输出：**


```
编译IR字节码： foo_sample.cpp.bc
隐私合约(wasm)： foo_contract.cpp
JAVA数据接入代理代码： 当前java目录生产java sdk代理代码


```

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

### 接口定制说明

**input**:


```java
通过inputImpl方法传入数据参与计算的数据，可以直接通过ret_value给基础类型赋值，或者通过builder.set_XXX给 message 结构体进行赋值


```
**error**:


```java
计算过程出现的错误会通过该接口返回，用户可以在这里对错误信息进行处理


```
**result**：


```java
目前在计算参与Bob方能够通过该接口获得计算结果，其中`data`是明文的16进制串


```

### 更多示例
参考[MPCSamples.java](../../samples/mpc-data-sdk-client/src/main/java/net/platon/vm/mpc/MPCSamples.java)

## 隐私客户端SDK

说明：以下涉及的方法均在由`plang`编译获得代理类`ProxyXXX.java`中使用。

### 发起计算


```java
public String startCalc(Method method)
public String startCalc(Method method, int retry)


```
函数功能：
 * 通过该方法发起计算，并返回交易hash，根据该交易hash，可以获取计算任务ID、交易回执、结果密文等。

参数说明：

* `method` 发起计算调用的方法，Method定义在生成的代理类中；
* `retry` 重发次数，如果返回为null,会从发交易，直到返回的不为空，或者达到重发次数；默认retry为0;
* 返回交易hash；

### 获取任务ID


```java
public String getTaskId(String transactionHash)
public String getTaskId(String transactionHash, long timeout)


```
函数功能：
 * 根据交易hash，获取计算任务Id,后续可以通过任务Id查询结果。

参数说明：
* `transactionHash` 发起计算方法`startCalc`返回的交易哈希；
* `timeout` 超时时间，最小0，最大180s；
* 返回值为任务ID；

### 获取交易回执


```java
public TransactionReceipt getTransactionReceipt(String transactionHash);


```
函数功能：
 * 根据交易hash，获取交易回执。

参数说明：
* `transactionHash` 发起计算方法`startCalc`返回的交易哈希；
* 返回交易回执。

**注意：如果计算成功，返回的交易回执中logs参数不为空，否则表示计算失败，如下：**


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
* `transactionHash` 发起计算方法`startCalc`返回的交易哈希；
* `taskId` `getTaskId`返回的任务ID。
* `timeout` 超时时间，最小0，最大180s。
* 返回计算结果密文


### 获取结果明文


```java
public int getInt32(String cipher)
public long getInt64(String cipher)
// getUInt32 getUInt64 getBool getFloat getDouble getString ... 
public com.abc.sample.Samples.Foo getFoo(String cipher)
// ...


```
函数功能：
* 该类接口将获取到的密文进行解密之后转换为相应的基本类型，或者自定义的`protobuf`结构体。其中基本类型的的转换在`mpc-proxy-sdk`中已经进行封装，而自定义的`protobuf`结构体的转换在`plang`编译获得的java文件中生成，可以直接使用。
  

参数说明：
* `cipher` 密文字符串。
* 返回基本类型或结构体。

**注意：由于该方法中需要将密文解析为明文，需要计算发起方才能通过该方法获取明文，非计算发起方调用此方法会因无法解密而报错！**

### 其他


```java
public static void showMethodMap()


```
函数功能：     
* 显示函数方法名，函数原型，函数枚举。


```java
public String getPlainText(String cipher)


```
函数功能：     
* 传入密文，获取结果明文(16进制字符串)。这个在知道私钥和密文的情况下即可使用。