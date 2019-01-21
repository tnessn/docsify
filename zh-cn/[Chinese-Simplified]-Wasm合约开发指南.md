## Wasm合约概述

### Wasm合约简述
`PlatON` 平台所采用的智能合约体系完全不同于传统智能合约。其致力于服务计算世界，提出面向计算的 *Wasm合约*。

PlatON 本质上是一个去中心化拓扑结构下的 `Serverless` 架构分布式服务平台。Wasm合约则是部署于其上的 FaaS (Functions as a Service) 应用。用户只需要提交合约代码到 PlatON 平台，就可以对外发布服务，且只需为执行代码过程中消耗的资源支付能量。

Wasm合约采用`c++`语法进行合约的编写，为了更好与PlatON平台的进行整合，同时提供了丰富的类库 —— [Wasm合约内置库](https://pwasmdoc.platon.network/modules.html)。结合二者可以让我们更容易的编写出高性能、功能强大的智能合约。

### PlatON 虚拟机

PlatON 虚拟机 `pWASM` 是Wasm合约的运行环境。具有沙盒化运行，完全隔离，合约逻辑中无法访问网络、文件系统和其他外部资源的特点。甚至Wasm合约之间的访问也是受限的。

本文档主要介绍Windows环境下的Wasm合约开发指南，其他环境开发指南会尽快推出，敬请关注。


## 开发环境配置

* [配置Wasm合约开发环境](_配置Wasm合约开发环境#%E9%85%8D%E7%BD%AEwasm%E5%90%88%E7%BA%A6%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83)

## 快速开始

* [编写第一个Wasm合约](_配置Wasm合约开发环境#%E7%BC%96%E5%86%99%E7%AC%AC%E4%B8%80%E4%B8%AAwasm%E5%90%88%E7%BA%A6)
* [合约发布及测试](_配置Wasm合约开发环境#%E5%90%88%E7%BA%A6%E5%8F%91%E5%B8%83%E5%8F%8A%E6%B5%8B%E8%AF%95)

## 常见问题

**合约无法发布问题**

**A**: 请确保配置文件中的能量设置合理，使用的账户已在节点侧解锁。

**更多问题**

如果你有其他问题，或者你的问题在这里找不到答案，请在此提交一个 [issue](https://github.com/PlatONnetwork/PlatON-Go/issues/new) 。
