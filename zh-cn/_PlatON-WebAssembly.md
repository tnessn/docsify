## WebAssembly 简介
WebAssembly（简称Wasm）是一种新的适合于编译到Web的，可移植的，大小和加载时间高效的格式。这是一个新的与平台无关的二进制代码格式。Wasm 可以由C++/Rust等高级语言编译而来。

## WebAssembly 在PlatON的实践
Wasm主要目标是解决JavaScript性能问题，但在区块链的实践当中，Wasm可以在智能合约的开发上带来更大的便利。Wasm 提供更加高效的执行环境，在智能合约的编写上，Wasm 可以利用C++/Rust等高级语言进行编写，利用已有的高级语言进行编写，降低编写的门槛，同时它们拥有丰富的类库，使得在合约编写上能够更为便利，不用在重新"造轮子"。

### 工具链
PlatON 目前选择C++作为智能合约的编写语言，针对C++提供以下工具链：

* clang : C/C++的前端编译器，负责生成LLVM中间字节码，生成后为`.bc`文件
* llvm-link : llvm链接器， 负责将`.bc`文件进行链接。主要是C/C++标准库的`.bc`文件与目标代码的`.bc`文件进行链接。
* llc : 负责将`.bc`文件编译到汇编文件`.s`
* platon-s2wasm(binaryen的s2wasm) : 负责将汇编代码生成为wast格式
*  platon-wast2wasm(binaryen的wasm-dis) : 负责将wast(文本格式)生成为wasm(二进制格式)

### 虚拟机
PlatON 采用[life][1]作为PlatON虚拟机,[life][2]是Wasm虚拟机的Go版本实现，继承自[wagon][3]。life相比较wagon在标准规范上面支持的较为全面。同时在引入外部函数时也较为灵活。作为[PlatON][4]的虚拟机,我们对此进行改造。实现链上的外部函数以及[Gas][5]的计算方式

  
### PlatON Wasm执行流程
![wasm_compile_pub_tx (1).jpg-68.8kB][6]

* 利用C++进行合约编写
* 利用platon-abigen工具生成合约ABI文件
* 利用clang生成`.bc`文件
* 利用llvm-link 连接所有目标文件.bc
* 利用llc编译生成汇编文件.s
* 利用platon-s2wasm生成.wast文件
* 利用platon-wast2wasm生成.wasm文件
* 将编译后的ABI文件及Wasm文件利用改造后的Web3客户端发送到PlatON中
* 在合约的部署中会调用合约中init函数进行初始化
* 在执行交易中根据ABI的描述初始化启动参数，创建虚拟机进行执行

### PlatON Wasm优化
在进行Wasm开发中发现，虚拟机每次执行合约都会进行[Module][7]的解析。由于wasm文件通常较大，解析较为耗时。针对同一个智能合约，每次解析的Module都是一样的。为此，PlatON 为合约的Module的Module设计了缓存。缓存分为内存缓存与文件缓存。缓存的大体方案如下：

* 在每次发布合约时，解析Module，将Module存入到磁盘，并同时在内存中进行缓存
* 内存缓存被设计成为优先级队列
* 虚拟机每次启动时根据合约的地址加载对应的Module
* Module每次先在内存缓存中获取，如果没有则从磁盘中获取并加入到内存缓存中


  [1]: https://github.com/perlin-network/life
  [2]: https://github.com/perlin-network/life
  [3]: https://github.com/go-interpreter/wagon
  [4]: https://github.com/PlatONnetwork/PlatON-Go
  [5]: https://www.cryptocompare.com/coins/guides/what-is-the-gas-in-ethereum/
  [6]: http://static.zybuluo.com/tracebundy/r9699bipuourf5temlypw68x/wasm_compile_pub_tx%20%281%29.jpg
  [7]: https://webassembly.org/docs/modules/