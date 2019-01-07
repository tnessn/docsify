
Follow the appropriate link below to find installation instructions for your platform.
+ Installation Instructions for Linux/Unix
  - [Ubuntu](#Installing-on-Ubuntu)
  - Arch
  - FreeBSD
+ [Installation Instructions for Windows](#Installing-on-Windows)
+ Usage instructions for Docker 
 
## Installing on Ubuntu
We assumes that the installation directory is ~/platon-node. Make sure the platon-node directory has been created.


```
$ mkdir -p ~/platon-node


```

The Ubuntu runtime environment needs to meet the following requirements:
- System version: `Ubuntu 16.04.1 above`
- go language development kit: `go(1.7+)`

1. First check if the go language development kit is installed.


```
$ go version


```
If the prompt is empty, it means that golang is not installed. If so, install it with the following steps.
If golang is already installed and the version is 1.7 and above, skip the following steps.
If golang version is lower than 1.7, you need to execute `whereis go` to find the installation directory and delete it before installing.

2. Download golang package


```
$ wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz


```

3. Extract to `/usr/local/`


```
$ sudo tar -C /usr/local/ -xzf go1.11.1.linux-amd64.tar.gz


```

4. Modify environment variables，open file `~/.bash_profile` and add the following：


```
export PATH=$PATH:/usr/local/go/bin


```

5. Make environment variables take effect


```
$ source ~/.bash_profile


```

6. Verify that `golang` was installed successfully:


```
$ go version
Go version go1.11.4 linux/amd64


```
If the prompt is not empty, it means that golang was installed successfully. 

There are three installation methods on Ubuntu.

### Installing official binary 
Ubuntu version of PlatON have been pre-built and can be downloaded [here](https://download.platon.network/platon-linux-amd64). After downloading, you can copy it to `~/platon-node` directory and use it directly.


```
$ wget https://download.platon.network/platon-linux-amd64
$ mv platon-linux-amd64 ~/platon-node/platon


```

### Installing using PPA
Todo.

### Installing from source
The Ubuntu build environment needs to meet the following requirements:
- System version: `Ubuntu 16.04.1 above`
- git：`2.19.1 above`
- Compiler: `gcc(4.8.0+)`
- go language development kit: `go(1.7+)`

The PlatON compilation and installation process is as follows:

1. Upgrade gcc:


```
$ gcc --version


```
If the gcc version is lower than 4.8.0, you need to upgrade it:


```
$ sudo apt-get update
$ sudo apt-get upgrade gcc


```

2. Clone `platon` source code from the official repository:


```
$ git clone https://github.com/PlatONnetwork/PlatON-Go.git


```

4. Execute the following commands to compile the `platon` executable:


```shell
$ cd PlatON-Go
$ find ./build -name "*.sh" -exec chmod u+x {} \;
$ make all


```

5. Copy the compiled `platon` file to `~/platon-node`


```
$ mv build/bin/platon ~/platon-node


```

## Installing on Windows
We assumes that the installation directory is D:\platon-node. Make sure the platon-node directory has been created.


```
C:\Users\PlatON> mkdir D:\platon-node


```

The Windows runtime environment needs to meet the following requirements:
- go language development kit: `go(1.7+)`
- mingw: `mingw(V6.0.0)`

Open the command prompt with administrator privileges. Install golang and mingw using Chocolatey. If you don't have Chocolatey, you can follow the instructions on [Chocolatey](https://chocolatey.org).


```
C:\Users\PlatON> choco install golang
C:\Users\PlatON> choco install mingw


```
Most of the software installed with the Chocolatey package manager use the default installation path, although some publishers override the default settings in their software. Installing these packages will modify the PATH environment variable. The final installation path can be found in PATH. After installation, please make sure that the installed version of Go is 1.7 (or higher).


```
C:\Users\PlatON> go version
go version go1.11.4 windows/amd64


```

There are three installation methods on Windows.

### Installing official binary 
Windows version of PlatON have been pre-built and can be downloaded [here](https://download.platon.network/platon-windows-amd64.exe). After downloading, you can copy it to `D:\platon-node` directory and use it directly.


```
C:\Users\PlatON> move platon-windows-amd64.exe D:\platon-node\platon.exe


```

### Installing Chocolatey
Todo.

### Installing from source
The Windows build environment needs to meet the following requirements:
- git:`2.19.1`
- go language development kit: `go(1.11.1+)`
- mingw: `mingw(V6.0.0)`

The compilation process is as follows:

1. We use the Chocolatey package manager to install the required build tools. If you don't have Chocolatey, follow the instructions on [Chocolatey] (https://chocolatey.org).

2. Open a command prompt with administrator privileges and install git:


```
C:\Users\PlatON> choco install git


```
Most of the software installed with the Chocolatey package manager use the default installation path, although some publishers override the default settings in their software. Installing these packages will modify the PATH environment variable. The final installation path can be found in PATH. 

3. Set up Go workspace:

Run the following steps in a new prompt without administrator privileges. If your Go workspace is *%USERPROFILE%*, skip to step 4:


```
C:\Users\PlatON> set "GOPATH=%USERPROFILE%"
C:\Users\PlatON> set "Path=%USERPROFILE%\bin;%Path%"
C:\Users\PlatON> setx GOPATH "%GOPATH%"
C:\Users\PlatON> setx Path "%Path%"


```

If you see the following warning message as a result of above command, it means the `setx` command truncated Path/GOPATH:


```
WARNING: The data being saved is truncated to 1024 characters.


```

If this happens, abort and make more room in Path and try again.

4. Get `platon` source code:


```
C:\Users\PlatON> mkdir %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go %GOPATH%\bin
C:\Users\PlatON> git clone https://github.com/PlatONnetwork/PlatON-Go.git
C:\Users\PlatON> xcopy /S PlatON-Go %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go
C:\Users\PlatON> cd %GOPATH%\src\github.com\PlatONnetwork\PlatON-Go


```

5. Start building:

The command to compile `platon` is:


```
C:\Users\PlatON\src\github.com\PlatONnetwork\PlatON-Go> go install -v ./cmd/platon


```

6. Copy the compiled `platon.exe` file to `D:\platon-node`


```
C:\Users\PlatON\src\github.com\PlatONnetwork\PlatON-Go> move %GOPATH%\bin\platon.exe D:\platon-node


```