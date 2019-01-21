## 配置Wasm合约开发环境

### 第三方工具包安装

Windows合约开发环境需要符合以下条件：

* [cmake 3.0+](https://cmake.org/download/) 用于生成构建文件 
* [mingw 6.0.0+](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-posix/seh/x86_64-8.1.0-release-posix-seh-rt_v6-rev0.7z/download) 用于编译构建文件 
* [git-bash](https://git-scm.com/downloads) `window`环境下强烈建议安装 `gitbash`，很多命令执行需要借助此工具完成


1. CMake安装

一键式安装，根据提示逐步安装即可。安装过程中，注意允许添加PATH环境变量。如果错过，请手动将cmake下bin文件夹所在路径添加到PATH。

2. mingw安装

下载解压后为绿色版本，直接配置`PATH`环境变量后即可使用。


**Linux**合约开发环境需要符合以下条件：

* [cmake 2.8+](https://cmake.org/download/) 


### 下载pWASM开发工具包

`Window` 版本pWASM开发工具: [https://download.platon.network/0.3/pwasm-windows-x86_64-0.3.0.zip](https://download.platon.network/0.3/pwasm-windows-x86_64-0.3.0.zip)

`Linux` 版本pWASM开发工具: [https://download.platon.network/0.3/pwasm-linux-amd64-0.3.0.tar.gz](https://download.platon.network/0.3/pwasm-linux-amd64-0.3.0.tar.gz)

pWASM开发工具包为一个压缩包，下载完成后解压到工作目录，如`D:\`（以下关于合约环境搭建的操作均在该目录下完成）。

`Linux` 下载
```shell
$ wget https://download.platon.network/0.3/pwasm-linux-amd64-0.3.0.tar.gz
$ tar -zxvf pWASM-linux.tar.gz
$ mv pWASM-linux pWASM
```

解压后所有文件均位于`pWASM`目录下，该目录文件结构如下：

```txt
├── bin
│   └── ctool
├── boost
├── CMakeModules
├── external
├── libc
├── libc++
├── platonlib
├── rapidjson
├── docs
├── script
├── user
├── CMakeLists.txt
└── README.md
```

部分文件夹功能概述：
* `ctool`  用于发布调用`wasm`合约
* `external` 用于存放编译过程中需要的二进制包，主要包含`abi`与`bin`文件的生成工具链
* `user` 能量合约范例，仅供参考。同时也用于存放脚本自动生成合约模板文件
* `platonlib` PlatON 专供的合约与区块链交互的库。具体参考：[Wasm合约内置库](https://pwasmdoc.platon.network/)


## 编写第一个Wasm合约

下面我们演示如何创建一个自己的 "hello world" 合约，主要的功能是完成智能合约的数据上链存储与查询功能，并通过事件把结果返回给调用者。

### 创建Wasm合约工程

1.编译环境使用了 `cmake` 的规范，构建前请确保本机的 `cmake` 环境已准备就绪。

2.Windows下执行 `git-bash.exe` 文件以打开 `git-bash` 窗口。假设工程根目录 {pWASM} 为Windows对应目录 `D:\pWASM`。

3.使用脚本 `{pWASM}/script/autoproject.bat` (linux下脚本为：`{pWASM}/script/autoproject.sh`) 可以快速构建工程目录。命令如下：

执行该脚本前有几点需要注意：

* 首次执行时确保工作目录下不存在`build`目录，如果存在请删除。
* 非首次执行或之前执行过程中存在意外中断，请删除`build`目录。
* 脚本执行可通过参数实现对于希望重新构建而不生成新合约的场景。

**执行脚本，重新构建，不生成新合约**

Windows
```shell
$ cd {pWASM}
$ ./script/autoproject.bat . 
```

Linux 
```shell
$ cd {pWASM}
$ ./script/autoproject.sh . 
```

**执行脚本，生成新合约并构建**

Windows 
```shell
$ cd {pWASM}
$ ./script/autoproject.bat . firstdemo 
```

Linux 
```shell
$ cd {pWASM}
$ ./script/autoproject.sh . firstdemo 
```

该 `firstdemo` 工程会默认在 `user` 目录生成。此时新生成的工程目录结构为：

```text
├──build/ 
├──user/
  ├──firstdemo/
  ├── firstdemo.cpp
  └── CMakeLists.txt 
```

目录文件简述：
* `firstdemo.cpp` 为合约源文件，新的合约逻辑可以基于此文件进行更改
* `CMakeLists.txt` CMake 的描述文件

**注意：默认生成的文件内容为空，后续的合约内容需要自行编写。下面介绍一个简单示例。**

### 合约代码示例

```c++
#include <stdlib.h>
#include <string.h>
#include <string>
#include <platon/platon.hpp>

namespace demo {
    class FirstDemo : public platon::Contract
    {
        public:
            FirstDemo(){}
			
            /// 实现父类: platon::Contract 的虚函数
            /// 该函数在合约首次发布时执行，仅调用一次
            void init() 
            {
                platon::println("init success...");
            }

            /// 定义Event.
            PLATON_EVENT(Notify, uint64_t, const char *)

        public:
            void invokeNotify(const char *msg)
            {	
                // 定义状态变量
                platon::setState("NAME_KEY", std::string(msg));
                // 日志输出
                platon::println("into invokeNotify...");
                // 事件返回
                PLATON_EMIT_EVENT(Notify, 0, "Insufficient value for the method.");
            }

            const char* getName() const 
            {
                std::string value;
                platon::getState("NAME_KEY", value);
                // 读取合约数据并返回
                return value.c_str();
            }
    };
}

// 此处定义的函数会生成ABI文件供外部调用
PLATON_ABI(demo::FirstDemo, invokeNotify)
PLATON_ABI(demo::FirstDemo, getName)
```

### 编译合约

1. 合约编译是为了将`.cpp` 源码编译成所需要的`abi`与`wasm`二进制文件（虚拟机指令集）。
2. 使用示例中的合约替换默认生成的 `firstdemo.cpp` 文件，完成合约部署。
3. 执行编译命令：

Windows 
```shell
$ cd {pWASM}/build/
$ mingw32-make.exe 
```

Linux  
```shell
$ cd {pWASM}/build/
$ make  
```

执行完成后，在`build/user`目录下会生成测试目录：`firstdemo`，同时也生成了目标文件，例如:

```text
├──build/ 
  ├──user/
    ├──firstdemo/
      ├── firstdemo.s
      ├── firstdemo.bc 
      ├── firstdemo.wast 
      ├── firstdemo.wasm 
      ├── firstdemo.cpp.abi.json
      ├── firstdemo.cpp.bc 
      └── cmake_install.cmake 
```

主要文件简介：

* `.bc` 连接标准库后的LLVM字节码文件，含所有依赖库；
* `.json` 合约对应的接口描述文件；
* `.s` 汇编文件；
* `.wasm` 合约编译后的二进制文件，字节码指令集； 
* `.wast` 合约编译后的可被认为读懂的指令文件；
* `.cpp.bc` ；LLVM 字节码文件，仅包含合约代码本身的；

**注意：**
上述目录中，在进行合约发布需要用到的是 `firstdemo.wast` 和 `firstdemo.cpp.abi.json`。

**提示：**
如果在编译中出现如下错误信息，请不要惊慌，正常输出。

```code
Could not auto-detect compilation database for file "/workspace/pWASM/hello/hello.cpp"
No compilation database found in /workspace/pWASM/hello or any parent directory
json-compilation-database: Error while opening JSON database: No such file or directory 
```

## 合约发布及测试

### 合约发布测试工具

**ctool工具用法**：

Windows 
```bash
$ mv ctool-windows-amd64.exe ctool.exe
$ ./ctool.exe <command> [--addr contractAddress] [--type txType(default:2)] [--func funcInfo] --abi <abi_path> --code <wasm_path> [--config <config_path>]
```

Linux  
```bash 
$ wget https://download.platon.network/ctool-linux-amd64
$ mv ctool-linux-amd64 ctool
$ ./ctool <command> [--addr contractAddress] [--type txType(default:2)] [--func funcInfo] --abi <abi_path> --code <wasm_path> [--config <config_path>]
```

**参数：**

* `command` ：待执行的命令。更多功能选项请使用 `./ctool.exe help` 查询
* `abi_path` ：ABI文件的路径。示例中为：`firstdemo.cpp.abi.json` 文件所在路径
* `wasm_path` ：WASM文件路径。示例中为：`firstdemo.wasm` 文件所在路径
* `config_path` ：配置文件路径。配置文件用于设置[PlatON节点]([Chinese-Simplified]-快速指南)及账户信息

**提示1：**
如果命令行中未明确指明配置文件路径，则会在当前工作路径读取文件：`config.json`。 

**提示2：**
发布合约到PlatON网络，需要连接到节点，并保证发布合约的账户已进行了解锁操作，且没有超时。

**提示3：**
如果命令不能执行，请确保脚本具有执行权限，可使用命令：`chmod +x ctool` 进行授权。

配置文件示例：

```JSON
{
  "url":"http://127.0.0.1:6789",
  "gas":"0x333330",
  "gasPrice":"0x333330",
  "from":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219"
} 
```

配置文件字段简述：
- `url` PlatON节点开放的`JSON-RPC`地址信息； 
- `from` 发布合约者的账户地址。


### 发布合约

1.你既可以把Wasm合约发布到[测试网络]([Chinese-Simplified]-快速指南#%e8%bf%9e%e6%8e%a5%e5%88%b0%e8%b4%9d%e8%8e%b1%e4%b8%96%e7%95%8c%e6%b5%8b%e8%af%95%e7%bd%91%e7%bb%9c)，也可以把合约发布到我们的PlatON主网(暂未开放)，单前提是你已经在网络中拥有了账号，并持有一定的Energon。如果没有，你也可以自行搭建一个[私有网络]([Chinese-Simplified]-私有网络)以供测试。

2.连接到PlatON节点，连接到节点的操作请[查看这里]([Chinese-Simplified]-快速指南#%e8%bf%9e%e6%8e%a5%e5%88%b0%e7%bd%91%e7%bb%9c)。

3.确保节点启动时开启 `personal` 相关RPC接口。解锁持有Energon的账户，输入账户密码

```
> personal.unlockAccount("your-account")
Unlock account 0x2d616026162ad2d513691b790806fa6f6bc3c2ef
Passphrase:
true
```

4.进入编译目录`{pWASM}/build/user/firstdemo`，在此目录下创建 `config.json` 配置文件，并拷贝 `ctool.exe` (linux拷贝`ctool`) 到当前目录。然后执行：

Windows 
```shell
$ cd {pWASM}/build/user/firstdemo 
$ ./ctool.exe deploy --abi ./firstdemo.cpp.abi.json --code ./firstdemo.wasm --config ./config.json 
```

Linux 
```shell
$ cd {pWASM}/build/user/firstdemo 
$ ./ctool deploy --abi ./firstdemo.cpp.abi.json --code ./firstdemo.wasm --config ./config.json 
```

5.命令执行成功后，会返回合约地址：
```
trasaction hash: 0xdb0f9a28fcd447702e8d5961f47144d1ea830979e3c984acc8f72c0dca8bdcfc
contract address: 0x43355c787c50b647c425f594b441d4bd751951c1
```
如果下一步需要进行合约调用则需要记录该地址信息！

### 测试合约

合约发布成功后，接下来进行合约调用，示例合约对外暴露了两个方法:`invokeNotify` 和 `getName`，此处先发送交易调用`invokeNotify`，然后调用`getName`获取值。操作如下：

**发送交易**

Windows 
```shell 
$ cd {pWASM}/build/user/firstdemo
$ ./ctool.exe invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'invokeNotify("HelloWorld")' --abi ./firstdemo.cpp.abi.json --config ./config.json
```

Linux  
```shell 
$ cd {pWASM}/build/user/firstdemo
$ ./ctool invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'invokeNotify("HelloWorld")' --abi ./firstdemo.cpp.abi.json --config ./config.json
```

**交易查询**

Windows
```shell 
$ cd {pWASM}/build/user/firstdemo
$ ./ctool.exe invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'getName()' --abi ./firstdemo.cpp.abi.json --config ./config.json
```

Linux 
```shell 
$ cd {pWASM}/build/user/firstdemo
$ ./ctool invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'getName()' --abi ./firstdemo.cpp.abi.json --config ./config.json
```

> 实际操作中 `invokeNotify` 传入的参数在 `getName` 中查询出来，表示合约发布、交易、查询过程已全部成功。
