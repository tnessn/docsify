**This document provides `PlatON` installation guide for different environments， After installation, you can refer to the document [Private Network](en-us/basics/%5BEnglish%5D-Private-Networks) to start.**

Follow the appropriate link below to find installation instructions for your platform.
+ Installation Instructions for Linux/Unix
  - [Ubuntu](#Installing-on-Ubuntu)
+ [Installation Instructions for Windows](#Installing-on-Windows)
+ [Running in Docker Environment](#Running-in-Docker-Environment)
 
## Installing on Ubuntu

The Ubuntu runtime environment needs to meet the following requirements:
- System version: `Ubuntu 16.04.1 or above` 

There are four ways of installation on Ubuntu: 
- binaries package
- PPA source
- debian package
- source code

### Binary package based installation

The official binary package file download link for ubuntu is：[https://download.platon.network/0.3/platon-ubuntu-amd64-0.3.0.tar.gz](https://download.platon.network/0.3/platon-ubuntu-amd64-0.3.0.tar.gz)

```bash
$ wget https://download.platon.network/0.3/platon-ubuntu-amd64-0.3.0.tar.gz
$ tar -xvzf platon-ubuntu-amd64-0.3.0.tar.gz
```

The extracted files should be as following:
- `platon` client executable file
- `ethkey` key generator
- `ctool`  wasm contract deploy kit

### PPA based installation

Add PPA to your system and update：

```
# add PPA
$ sudo add-apt-repository ppa:platonnetwork/platon
$ sudo apt-get update

# install platon
$ sudo apt-get install platon-all
```

After the installation, the binaries and other components of the package should be installed to `/usr/bin/`

### Debian package based installation

Download the `.deb` package and then install.

```bash
# download
$ wget https://download.platon.network/0.3/platon-ubuntu-amd64-0.3.0.deb

# install
$ sudo dpkg -i platon-ubuntu-amd64-0.3.0.deb
```

After the installation, the binaries and other components of the package should be installed to `/usr/bin/`

### Source code based installation

The Ubuntu build environment needs to meet the following requirements:
- System version: `Ubuntu 16.04.1 or above`
- git：`2.19.1 or above`
- Compiler: `gcc(4.9.2+)`
- go language development kit: `go(1.7+)`

>**Note**: Make sure the compilation environment requirements are met！

The PlatON compilation and installation process is as follows:

#### 1. Clone `platon` source code to local target folder:

```
$ git clone https://github.com/PlatONnetwork/PlatON-Go.git
```

#### 2. Compilation

##### Compiling `Platon` without `mpc` capability by default

```bash
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all
```

##### Compiling `Platon` with `mpc` capability 

To enable `MPC` function on `platon`, compile `MPC VM` module and link it to the platon. Steps are as follows:

- Compiling `MPC VM`

Please referring to [Privacy Contract VM Compilation](https://github.com/PlatONnetwork/privacy-contract-vm#building--installing).
Assuming that the compilation directory is `home/path/to/mpcvm/build`, the compilation will output the `MPC VM` runtime libraries to `home/path/to/mpcvm/build/lib`.

- Setup environment

Append the compiled `MPC VM` libraries path `~/home/path/to/mpcvm/build/lib` to current user libraries environment variable:

```bash
grep "export LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib" ~/.bashrc || echo "export  LD_LIBRARY_PATH=\$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib" >> ~/.bashrc

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:~/home/path/to/mpcvm/build/lib
```

- Compiling `platon`

```bash
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all-with-mpc
```
After compilation, the `platon`、 `ethkey` and `ctool` executable files will be generated in the `PlatON-Go/build/bin` directory，and then copy these executable files to your own working directory.

>**Hint**：
>MPC is secure multi-party computing feature supported by the Platon platform for privacy calculations. **Only Ubuntu supported now**. For more information about MPC, please refer [Reference](https://github.com/PlatONnetwork/wiki/wiki/%5BEnglish%5D-PlatON-Privacy-Contract-Guide)


## Installing on Windows

The Windows environment supports three installation modes:
- Binary package
- Chocolatey installation
- source code

### Binary package based installation

Windows version of the Platon binary download link is: [https://download.platon.network/0.3/platon-windows-x86_64-0.3.0.zip](https://download.platon.network/0.3/platon-windows-x86_64-0.3.0.zip) download. No installation is required after downloading, and it can be used directly by decompression.

The extracted files should be as following:
- `platon` client executable file
- `ethkey` key generator
- `ctool`  wasm contract deploy kit

### Installation via Chocolatey

We use the Chocolatey package manager to install the required build tools. If you don't have Chocolatey, follow the instructions on [https://chocolatey.org](https://chocolatey.org).

Start PowerShell as an administrator and install Platon using the choco command:

```
choco install platonnetwork --version=0.3.0
```

You will find `platon`,`ethkey` in the default installation path `C:\ProgramData\chocolatey\bin`.

### Source code based installation

The Windows build environment requires:
- git:`2.19.1`
- go language development kit: `go(1.11+)`
- mingw: `mingw(V6.0.0)`

> Note: You can install the above compilation environment by yourself. Before compiling the `Platon` source code, make sure that the above environment works properly. The `chocolatey` installation can also be used, referring to the following ways:


#### 1. Install compilation environment

Start PowerShell as an administrator and install Platon using the choco command:

```
// install git
choco install git
// install golang
choco install golang
// install mingw
choco install mingw
```

Most of the software installed with the Chocolatey package manager use the default installation path, although some publishers override the default settings in their software. Installing these packages will modify the PATH environment variable. The final installation path can be found in PATH. 

#### 2. Get `PlatON` source code

Create `src/github.com/PlatONnetwork/` and `bin` directories under the current `%GOPATH%` directory and clone the source code of `PlatON-GO` under the `PlatONnetwork` directory:

```
git clone https://github.com/PlatONnetwork/PlatON-Go.git
```

#### 3. Compile

Execute the compilation command under the source directory `PlatON-GO`, as follows:

```
go run build/ci.go install ./cmd/platon
```

After compilation, the `platon`、 `ethkey` and `ctool` executable files will be generated in the `PlatON-Go/build/bin` directory，and then copy these executable files to your own working directory.


## Running in Docker Environment

#### Install docker

The docker installation is relatively simple, you can refer to the following two ways to install:

- Installation from [Aliyun image](https://yq.aliyun.com/articles/110806)
- Installation From [Docker's official image](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

#### Run Container

- Pull Platon Image
 
```bash
$ sudo docker pull platonnetwork/platon:tag #例如: platonnetwork/platon:0.3.0
```

- Run Platon Container

Start the `Platon` node in the container by executing the following commands. The default RPC port is 6789 and the P2P port is 16789:

```bash
$ sudo docker run -d platonnetwork/platon:tag
```

To open port mapping, execute the following commands:

```bash
$ sudo docker run -d -e PLATONIP="192.168.120.20" -p 6789:6789  -p 16789:16789 --name platon platonnetwork/platon:0.3.0
```

`PLATONIP`is the local server address.

Execute the following commands to enter the container:

```bash
$ sudo docker exec -it container_name bash #container_name is the container name or id.
```

Execute a command to enter the platform's JS terminal(The default directory is under /opt/node when you enter the container):

```bash
# platon attach ipc:./data/platon.ipc
```

Note: If not enable port mapping, you can only enter into the JS terminal through the above commands in the container. After opening the port mapping, you can enter the JS terminal locally through the following commands:

```bash
# platon attach http://PLATONIP:6789
```

Start and stop the platform service in the container using the following commands:

```bash
# supervisorctl start platon 
# supervisorctl stop platon 
# supervisorctl restart platon 
```

If the above command is invalid, use the following command：

```bash
# /etc/init.d/supervisor stop
# /etc/init.d/supervisor start
```

** Note: The data directory of Platon in the container is located in `opt/node` directory. By default, the system has only one wallet account, and the password is 123456**.