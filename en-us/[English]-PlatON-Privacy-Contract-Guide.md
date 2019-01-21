# Privacy Contract Developer Guide

This document provides specifications for writing privacy contracts, including basic data structures, function definitions, custom algorithms, and instruction of compilation.

## Privacy Contract

The privacy contract is one kind of meta-smart contract Sophia defined in whitepaper(Note 1), developed in C++ language, running in the privacy contract virtual machine (PCVM) of the PlatON public chain node. With Secure Multi-Party Computing (MPC) embeded, the privacy contract provides privacy protection for participant's data and secure multi-party computing services.

The PlatON public chain encapsulates the basic data types for privacy computing and provides secure operations for these data types. Users can freely use these data types within their privacy algorithms. The PlatON public chain designs and provides a privacy calculation security protocol to transmit data and calculation results, enables the participants implementation of secure computing without revealing actual data.

The current version only provides specifications for privacy contracts of two parties, three-party or more privacy contract guide will be released later.

## Dependent components

### PlatON Privacy Computing Component Library

Providing privacy calculation related header files, etc., has been built into the compiler.

### Google Protobuf Library

Compile the .proto file into the corresponding encoding and decoding header files and source files, and provide the google protocol buffer message packing and unpacking capabilities.

### Clang compiler

The Clang compiler compiles privacy contract in C++ into an IR file (compiled intermediate file) or a bytecode file for JIT virtual machine.

### Plang compiler

Plang is a custom compiler front end for PlatON. It compiles the privacy contract and outputs as algorithm IR (or bytecode), wasm smart contract, JAVA agent.

The Plang compiler depends on Clang at runtime, it needs to be installed in the same directory as Clang. We suggest copy Plang to the same directory as Clang.

## Privacy Calculation Basic Data Types

   The privacy contract supports 8-bits, 16-bits, 32-bits, 64-bits integers and other intergers in any length. Privacy data is uniformly represented by the **Privacy Calculation** operation type (**emp::Integer**), and used in multi-party secure computing based on MPC protocol.
    Assume two parties are Alice and Bob. The arithmetic variables supported by the privacy calculation include: **+, -, *, /, |, &, ^, %, shift**, etc.


Let's take the 32-bits integer privacy addition as an example:

1. Declare a 32-bits signed integer ``privacy calculation variable'`

```cpp
emp::Integer v1(123, ALICE);//Declare privacy calculation variable for Alice
emp::Integer v2(456, BOB);//Declare privacy calculation variable for Bob
```

2. Two parties privacy addition

```cpp
emp::Integer result = v1 + v2; //Private addition
```

3. Get the decryped value from result

```cpp
Int plain_result = result.reveal();//Alice and Bob both get the result of the calculation which is 579
```
***NOTE***:
     1. Privacy data variables of Alice and Bob must be same type and same length to have successful privacy calculation; otherwise, an error will be reported.
     2. Privacy calculation requires two existing parties, here one is Alice and the other is Bob, otherwise the calculation cannot be conducted.

## Privacy calculation data scope

The current privacy calculation can only support integer operation, ie **emp::Integer**. Privacy security calculations such as arithmetic operations, absolute values, and bit operations are provided.

### interface

```cpp
namespace emp
{
    Class Integer :
    Public Swappable<Integer>,
    Public Comparable<Integer>
    {
    Integer(const int8_t& value, int party = PUBLIC);
    Integer(const int16_t& value, int party = PUBLIC);
    Integer(const int32_t& value, int party = PUBLIC);
    Integer(const int64_t& value, int party = PUBLIC);

    Integer(unsigned int length, const std::string& value, int party = PUBLIC);
    Integer(unsigned int length, const void * b);

    ~Integer();

    //Comparable
    Bit geq(const Integer & rhs) const;
    Bit equal(const Integer & rhs) const;

    //Swappable
    Integer select(const Bit & sel, const Integer & rhs) const;
    Integer operator^(const Integer& rhs) const;

    Int size() const;

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

    Bit& operator[](int index);
    Const Bit & operator[](int index) const;
}//emp
```

## Privacy calculation function definition

**Privacy calculation function definition**:

  The function that can participate in the privacy calculation has at least two or more input parameters, parameter 1 corresponds to Alice's encrypted data, parameter 2 corresponds to Bob's encrypted data, and others are additional parameters.

**Function definition prototype**:

```cpp
return_type function_name(declare_type in1, declare_type in2, ...)
```
**meaning**:
```
return_type: return data type
Declare_type: C++ primitive type or type defined by Google Protobuf
in1: encryptd data from Alice
in2: encryptd data from Bob
```
**note**:
1. Input parameter types and return type must be in below scope:
- Basic types: char, unsigned, int, short, long, char, float, double, ...;
- String: std::string ;
- User-defined type: type defined by Google Protocol Buffer message;
2. Coding guide of return type of Privacy calculation function:
- The return value type should be Google Protocol Buffer types, including basic type, string, user-defined types
- Input parameters types should be Google Protocol Buffer types as well, including basic types, string, user-defined types (using java sdk external to directly use basic types, no need to display packaged as proto type)

-----------------

## Description of how privacy function works
The following code describes a privacy calculation function implementation which uses a user-defined type as a parameter.

1. Define a user-defined type Foo (use Google Protobuf defination)

```protobuf
syntax = "proto3";
message Foo {
	int32 item1 = 1;
	int32 item2 = 2;
	string info = 3;
}
```

2. Define the privacy calculation function

Here, the function implemented as an absolute value calculation. The encrypted input values ​​of Alice and Bob will not be leaked during the process, and final result will be returned as foo of user defined type Foo.

	code show as below:
```cpp
/**
* Perform the absolute value calculation of item1 and item2 input by Alice, then compare with Bob's input respectively. The calculation result returns as type foo.
*/
Foo foo_abs(const Foo& in1, int32_t in2, const std::string& extra)
{
    Foo foo = in1;
    emp::Integer item1(in1.item1(), ALICE);//Alice's input item1
    emp::Integer item2(in1.item2(), ALICE);//Alice's input item2
    emp::Integer v2(in2, BOB);//Bob's input
    emp::Integer ret1 = (item1 - v2).abs();//absolute value privacy calculation
    emp::Integer ret2 = (item2 - v2).abs();//absolute value privacy calculation
    foo.set_item1(ret1.reveal());//update item1 of foo
    foo.set_item2(ret2.reveal());//update item2 of foo
    foo.set_info(extra);

    return foo;
}
```

3. Call the function

For example, Alice enters {1000, 100}, and Bob enters 900 to conduct privacy calculation defined by foo_abs.

Alice calls the function as below：
```cpp
 /**
 Prepare alice varible externally in advance
  alice.set_item1(1000);
  alice.set_item2(100);
 */
 Foo alice;
 int in2 = 0;//Leave Bob's input empty
 Foo result = foo_abs(alice, in2, "abs");//The value of parameter 1 is only known to Alice who doesn't have knowledge of parameter 2, parameter 2 is from Bob who is the independent privacy calculation peer on the other end
```

Bob calls the function as below：
```cpp
//Bob doesn't need to know Alice's input
Foo alice;
int in2 = 900;//Bob's external seperate input
Foo result = foo_abs(alice, in2, "abs");//The value of parameter 2 is only known to Bob who doesn't have knowledge of parameter 1 in reverse, parameter 1 is from Alice who is the independent privacy calculation peer on the other end
```
You may find out that Alice and Bob run the same code: ``Foo result = foo_abs(alice, in2)`` with their own input data while leaving the counter party input data as 0 by default.

## Procesure of Privacy Calculation Customization

Demonstrate how to write a privacy contract with custom input, detailed preparation, compilation, privacy calculation data access procedures, privacy contract release procedure, privacy calculation task initialization, and how to get calculation results

### Privacy Algorithm Writing

The privacy algorithm consists of two parts: the input data type definition and the algorithm implementation. Input data types must be built-in types and custom types (follow Google Protobuf); Algorithm implementation should a be self-defined implementation of the privacy calculation function, which uses the privacy calculation data type object to carry MPC computing results.

- **protobuf defination**：

```protobuf
syntax = "proto3";
message Foo {
	int32 item1 = 1;
	int32 item2 = 2;
	string info = 3;
}
```
Save the protobuf defination into msg.pb file and compile it:

```bash
> protoc msg.pb --cpp_out=./
```
Generate the msg.pb.h header file and msg.pb.cc.

- **Privacy contract source code**:

Write a privacy contract that contains the pb-generated files and the integer.h header file with a text editor.
THen write the algorithm function with an input Foo object contains two integers items1 and item2, and another input of integer, followed by absolute value calculation and returns results in a new Foo object.

Code as below:

```cpp

    #include "msg.pb.h"
    #include "integer.h"

    /**
    *  Perform the absolute value calculation between Alice's item1 and item2 against Bos's in2 respectively. The calculation result returns in a new Foo object.
    */
    Foo foo_abs(const Foo& in1, int32_t in2, const std::string& extra)
    {
        Foo foo = in1;
        emp::Integer item1(in1.item1(), emp::ALICE);//item1 from Alice
        emp::Integer item2(in1.item2(), emp::ALICE);//item2 from Alice
        emp::Integer v2(in2, emp::BOB);//in2 from Bob
        emp::Integer ret1 = (item1 - v2).abs();//Privacy absolute value calculation
        emp::Integer ret2 = (item2 - v2).abs();//Privacy absolute value calculation
        foo.set_item1(ret1.reveal());//Update item1 of new foo
        foo.set_item2(ret2.reveal());//Update item2 of new foo
        foo.set_infO(extra);

        return foo;
    }
```
Save file as foo_sample.cpp


- **Compilation**

Compile foo_sample.cpp with Plang

Configure the command parameter file:
```bash
config.json is configured as below：
{
     "invitor":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219",
     "parties":["0x60ceca9c1290ee56b98d4e160ef0453f7c40d219", "0x3771c08952f96e70af27324de11bb380ec388ec3"],
     "method-price":{"foo_abs":200},
     "profit-rules":{"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":1, "0x3771c08952f96e70af27324de11bb380ec388ec3":2},
     "urls":{"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219":"DirectNodeServer:default -h 10.10.8.155 -p 10001",
             "0x3771c08952f96e70af27324de11bb380ec388ec3":"DirectNodeServer:default -h 10.10.8.155 -p 10002"}
}
```

Execute command:

```bash
plang foo.proto foo_sample.cpp -config config.json
```
Outputs：
```
digest:
 * IR NAME: MPC8171688343840523029_bc
 * IR HASH: d06597222b3bc5674f10d33c8b100862
 * <p>
 * IR FUNC HASH(MD5)                 IR FUNC NAME    
 * 6f9bbe6c90391233e73816de5aff6ba1  foo_abs         
 * aec7576d649940857aca097770321722  operator&       
 * 65b42adf4b685c5b9f4dc98c9f4a36c5  MakeTa
```
The following files will be generated:
```
Compiling intermdiate file: foo_sample.cpp.bc
Privacy contract(wasm): mpcc.cpp
JAVA data access agent： MPCfoo_sample.java (uner folder code/java)
protobuf output files: msg.pb.h, msg.pb.cc
```
Please refer to [Plang compiler user manual] for more details

### Sample 1： Millionaires's problem
[Yao's Millionaires' problem] is a secure multi-party computation problem which was introduced in 1982 by Andrew Yao. The problem discusses two millionaires, Alice and Bob, who are interested in knowing which of them is richer without revealing their actual wealth. With [PlatON]**the privacy contract**, we can resolve the problem with no effort. The implementation of privacy contract is below:

```cpp
#include "integer.h"
/**
* 32-bits integer input by Alice, another 32-bits integer input by Bob, Alice's minus BOB's
*/
int Millionaire(int32_t in1, int32_t in2, const std::string& category)
{
    emp::Integer item1(in1, emp::ALICE);//item1 input by Alice
    emp::Integer item2(in2, emp::BOB);//item2 input by Bob
    emp::Integer ret = (item1 - item2);//compare
    return ret.reveal() > 0 ? 1 : 0;//get the result
}
```

### More samples and details
For more samples and privacy data providing services, please refer [privacy contract sdk]

[Google Protobuf]:https://developers.google.com/protocol-buffers/
[PlatON]:https://github.com/PlatONnetwork/wiki/
[Plang]:https://github.com/PlatONnetwork/private-contract-compiler/
[Yao's Millionaires' Problem]:https://en.wikipedia.org/wiki/Yao%27s_Millionaires%27_Problem
[Plang compiler user manual]:https://github.com/PlatONnetwork/wiki/plang-user-manual/
[privacy contract sdk]:https://github.com/PlatONnetwork/wiki/wiki/%5BChinese-Simplified%5D%E9%9A%90%E7%A7%81%E5%90%88%E7%BA%A6SDK%E6%8C%87%E5%8D%97