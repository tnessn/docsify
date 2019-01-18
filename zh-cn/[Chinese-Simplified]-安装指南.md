**本文档提供`PlatON`在不同环境的安装，安装完成之后需要启动节点请参考文档[私有网络](#https://github.com/PlatONnetwork/wiki/wiki/%5BChinese-Simplified%5D-%E7%A7%81%E6%9C%89%E7%BD%91%E7%BB%9C)。**

PlatON支持不同的运行环境。
+ Linux/Unix环境安装
  - [Ubuntu](#Ubuntu安装)
+ [Windows环境安装](#Windows安装)

## Ubuntu安装

Ubuntu环境要求：
- 系统版本：`Ubuntu 16.04.1 及以上`

Ubuntu环境支持以下四种安装方式： 

- 官方二进制包
- PPA源
- debian包
- 源码

### 官方二进制包下载

ubuntu版本官方二进制包文件下载地址：[https://download.platon.network/platon-ubuntu-amd64-bin.tar.gz](https://download.platon.network/platon-ubuntu-amd64-bin.tar.gz)


```bash
# 下载
$ wget https://download.platon.network/platon-ubuntu-amd64-bin.tar.gz

# 解压
$ tar -xvzf platon-ubuntu-amd64-bin.tar.gz


```

解压内容如下：

- `platon` PlatON客户端
- `ethkey` 公私钥对生成工具

### PPA源安装

添加`PPA`源并安装`platon`软件包，安装命令如下：


```bash
# 添加PPA
$ sudo add-apt-repository ppa:platonnetwork/platon
$ sudo apt-get update

# 安装PlatON包
$ sudo apt-get install platon-all


```

安装完成后，可执行程序将安装到： `/usr/bin/`

### debian包安装

下载和安装：


```bash
# 下载安装包 
$ wget https://download.platon.network/platon-all-ubuntu-amd64.deb

# 安装
$ sudo dpkg -i platon-all-ubuntu-amd64.deb


```

安装完成后，可执行程序将安装到： `/usr/bin/`

### 源码编译安装

Ubuntu编译环境要求：

- 系统版本：`Ubuntu 16.04.1 及以上`
- git：`2.19.1及以上`
- 编译器：`gcc(4.9.2+)`
- go语言开发包：`go(1.7+)`

> ***注意：保证满足编译环境要求***

执行以下步骤：

#### 1. 获取源码

源码地址：[https://github.com/PlatONnetwork/PlatON-Go.git](https://github.com/PlatONnetwork/PlatON-Go.git)，执行如下命令编译生成 `platon` 可执行文件


```
$ git clone https://github.com/PlatONnetwork/PlatON-Go.git --recurive


```

#### 2. 编译

##### 编译无`MPC`功能`platon`客户端


```bash
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all


```

##### 编译带`MPC`功能`platon`客户端

如需`platon`启用`MPC` 功能，请编译 `MPC VM`模块，并链接到platon中。步骤如下：

- 编译`MPC VM`

请参考[隐私合约虚拟机编译](https://github.com/PlatONnetwork/privacy-contract-vm#building--installing)。
假定编译目录为`~/home/path/to/mpcvm/build`，编译将输出`MPC VM`运行库到`~/home/path/to/mpcvm/build/lib`。

- 配置环境

将编译之后的`MPC VM`库`~/home/path/to/mpcvm/build/lib`路径添加到当前用户环境变量:


```bash
grep "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib" ~/.bashrc || echo "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib" >> ~/.bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib


```

- 编译`platon`


```bash
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all-with-mpc


```

>**提示**：

>`MPC`计算功能是`PlatON` 平台实现隐私计算提供的基础设施，**当前仅支持 Ubuntu 系统**。更多MPC相关请[参考这里](zh-cn/[Chinese-Simplified]-%e9%9a%90%e7%a7%81%e5%90%88%e7%ba%a6%e5%bc%80%e5%8f%91%e6%8c%87%e5%8d%97)

## Windows安装

Window环境支持以下三种安装方式：

- 官方二进制包
- Chocolatey安装
- 源码

### 官方二进制下载安装
Windows版本的`platon`二进制文件下载地址：[https://download.platon.network/platon-windows-amd64-bin.zip](https://download.platon.network/platon-windows-amd64-bin.zip)下载。下载后无需安装，直接解压即可使用。

解压内容如下：

- `platon` PlatON客户端
- `ethkey` 公私钥对生成工具

### Chocolatey安装

用管理员身份启动`PowerShell`,然后使用`choco`命令安装`platon`：


```
C:\Users\PlatON> choco install platon --version=0.2.0


```
`platon`,`ethkey`等将默认被安装到`C:\ProgramData\chocolatey\bin`目录。

### 源码编译安装

Windows编译环境需要符合以下条件：

- git：`2.19.1以上`
- go语言开发包：`go(1.7+)`
- mingw：`mingw（V6.0.0）`

说明：可自行安装以上编译环境，在编译`Platon`源码之前请确保以上环境可正常运行。也可使用`chocolatey`安装，可参考以下方式：

#### 1. chocolatey安装

我们使用chocolatey包管理器来安装所需的构建工具。如果你还没有chocolatey，可以按照[chocolatey](https://chocolatey.org)上的说明进行安装。

#### 2. 安装编译环境

用管理员身份启动`PowerShell`，然后执行以下命令：


```
// 安装git
choco install git
// 安装golang
choco install golang
// 安装mingw
choco install mingw


```

利用chocolatey包管理器安装的软件大部分有默认的安装路径，部分软件可能会有各种各样的路径，这取决于软件的发布者。安装这些包将修改Path环境变量。最后安装路径可查看PATH。安装完之后请确保已安装的Go版本为1.7（或更高版本）。

#### 3. 获取`PlatON`源码

在当前`%GOPATH%`目录下创建`src/github.com/PlatONnetwork/`和`bin`目录，在`PlatONnetwork`目录下克隆`PlatON-GO`的源码:


```
git clone https://github.com/PlatONnetwork/PlatON-Go.git


```

#### 4. 编译

在源码目录`PlatON-GO`下执行编译命令，如下：


```
go run build/ci.go install ./cmd/platon


```

编译完成之后在`PlatON-Go/build/bin`目录下会生成`platon`和`ethkey`可执行文件