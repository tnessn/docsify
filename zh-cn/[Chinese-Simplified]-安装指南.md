PlatON支持不同的运行环境。
+ Linux/Unix环境安装
  - [Ubuntu](#Ubuntu安装)
  - Arch
  - FreeBSD
+ [Windows环境安装](#Windows安装)
+ Docker环境运行

## Ubuntu安装

本文假设安装到~/platon-node目录下，请确保platon-node目录已经创建。


```
$ mkdir -p ~/platon-node


```

Ubuntu运行环境需要符合以下条件：
- 系统版本：`Ubuntu 16.04.1 及以上`
- go语言开发包：`go(1.7+)`

1. 先检查是否安装 go 语言开发包


```
$ go version


```

如果输出为空则表示go没有安装，需要按照以下步骤进行安装。

如果go版本已经安装并且版本是1.7及以上，则不需要执行以下步骤。

如果go版本低于1.7，需要执行`whereis go`查找go安装目录并删除，并按照以下步骤重新安装。

2. 下载 `go` 安装包


```
$ wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz


```

3. 解压到 `/usr/local/` 路径


```
$ sudo tar -C /usr/local/ -xzf go1.11.1.linux-amd64.tar.gz


```

4. 配置环境变量，打开`~/.bash_profile`并添加


```
export PATH=$PATH:/usr/local/go/bin


```

5. 环境变量生效


```
$ source ~/.bash_profile


```

6. 验证 `golang` 是否安装成功


```
$ go version
go version go1.11.4 linux/amd64


```

Ubuntu环境支持以下三种安装方式。

### 官方二进制下载安装
Ubuntu版本的`platon`可从[这里](https://download.platon.network/platon-linux-amd64)下载。下载后无需安装直接拷贝到`~/platon-node`目录即可使用。


```
$ wget https://download.platon.network/platon-linux-amd64
$ mv platon-linux-amd64 ~/platon-node/platon


```

### PPA安装
待补充。

### 源码编译安装
Ubuntu编译环境需要符合以下条件：
- 系统版本：`Ubuntu 16.04.1 以上`
- git：`2.19.1以上`
- 编译器：`gcc(4.9.2+)`
- go语言开发包：`go(1.7+)`

PlatON编译安装过程如下：

1.升级gcc


```
$ gcc --version


```

如果gcc版本低于4.9.2，需要升级gcc。


```
$ sudo apt-get update
$ sudo apt-get upgrade gcc


```

2. 安装git


```
$ sudo apt-get install git


```

3. 从官方源码库克隆 `platon` 源代码


```
$ git clone https://github.com/PlatONnetwork/PlatON-Go.git


```

4. 执行如下命令编译生成 `platon` 可执行文件


```shell
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all


```

>**提示**：
>MPC 计算功能是PlatON 平台支持的安全多方计算功能，为实现隐私计算提供基础设施。**当前仅支持 Ubuntu 系统**。更多MPC相关请[参考这里](zh-cn/[Chinese-Simplified]-%e9%9a%90%e7%a7%81%e5%90%88%e7%ba%a6%e5%bc%80%e5%8f%91%e6%8c%87%e5%8d%97)

若要在节点上启用 MPC 功能，需安装带 `MPC` 功能的 `platon` 二进制可执行程序。相应步骤如下：

- 下载 MPC 计算依赖库 [mpclib](https://download.platon.network/mpclib.tar.gz) 到启动节点的服务器，这里我们放在节点路径 `~/platon-node/` 下，解压文件。


```bash
$ pwd
/home/platon/platon-node
$ tar -xzvf mpclib.tar.gz


```

- 解压后，在`~/platon-node/` 路径下出现 mpclib 文件夹。

- 使用root用户或者sudo权限打开profile文件


```bash
$ vi /etc/profile


```

- 添加环境变量，其中，`XXXX` 在本机为 platon 用户。开发者需改为自己登录的用户。


```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/XXXX/platon-node/mpclib


```

- 使用 `make all-with-mpc` 替换 `make all` 命令编译


5. 将编译生成的`platon`文件拷贝到`~/platon-node`目录


```
$ mv build/bin/platon ~/platon-node


```

## Windows安装
本文假设安装到D:\platon-node目录下，请确保platon-node目录已经创建。


```
C:\Users\PlatON> mkdir D:\platon-node


```

Windows运行环境需要符合以下条件：
- go语言开发包：`go(1.7+)`
- mingw：`mingw（V6.0.0）`

用管理员权限打开命令提示符使用Chocolatey安装golang和mingw,如果你还没有chocolatey，可以按照[chocolatey](https://chocolatey.org)上的说明进行安装。


```
C:\Users\PlatON> choco install golang
C:\Users\PlatON> choco install mingw


```

利用chocolatey包管理器安装的软件大部分有默认的安装路径，部分软件可能会有各种各样的路径，这取决于软件的发布者。安装这些包将修改Path环境变量。最后安装路径可查看PATH。安装完之后请确保已安装的Go版本为1.7（或更高版本）。


```
C:\Users\PlatON> go version
go version go1.11.4 windows/amd64


```

Window环境支持以下三种安装方式。

### 官方二进制下载安装
Windows版本的`platon`可从[这里](https://download.platon.network/platon-windows-amd64.exe)下载。下载后无需安装直接拷贝到`D:\platon-node`目录即可使用。


```
C:\Users\PlatON> move platon-windows-amd64.exe D:\platon-node\platon.exe


```

### Chocolatey安装
待补充。

### 源码编译安装
Windows编译环境需要符合以下条件：
- git：`2.19.1以上`
- go语言开发包：`go(1.7+)`
- mingw：`mingw（V6.0.0）`

编译过程如下:

1. 我们使用chocolatey包管理器来安装所需的构建工具。如果你还没有chocolatey，可以按照[chocolatey](https://chocolatey.org)上的说明进行安装。

2. 用管理员权限打开命令提示符并安装git：


```
C:\Users\PlatON> choco install git


```
利用chocolatey包管理器安装的软件大部分有默认的安装路径，部分软件可能会有各种各样的路径，这取决于软件的发布者。安装这些包将修改Path环境变量。最后安装路径可查看PATH。

3. 设置Go工作区目录布局

以下步骤不需要管理员权限，请打开新的命令提示符。如果你的Go工作区目录为 *%USERPROFILE%* 请直接跳到第4步。


```
C:\Users\PlatON> set "GOPATH=%USERPROFILE%"
C:\Users\PlatON> set "Path=%USERPROFILE%\bin;%Path%"
C:\Users\PlatON> setx GOPATH "%GOPATH%"
C:\Users\PlatON> setx Path "%Path%"


```

如果在以上命令中，收到以下消息：


```
WARNING: The data being saved is truncated to 1024 characters.


```

那意味着setx命令将失败，并且继续将截断Path/GOPATH。如果发生这种情况，最好中止，并在Path中腾出更多空间尝试之后再试。

4. 获取platon源代码


```
C:\Users\PlatON> mkdir %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go %GOPATH%\bin
C:\Users\PlatON> git clone https://github.com/PlatONnetwork/PlatON-Go.git
C:\Users\PlatON> xcopy /S PlatON-Go %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go
C:\Users\PlatON> cd %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go


```

5. 开始构建

编译platon的命令是：


```
C:\Users\PlatON\src\github.com\PlatONnetwork\PlatON-Go> go install -v ./cmd/platon


```

6. 将编译生成的`platon.exe`文件拷贝到`D:\platon-node`目录


```
C:\Users\PlatON\src\github.com\PlatONnetwork\PlatON-Go> move %GOPATH%\bin\platon.exe D:\platon-node\


```