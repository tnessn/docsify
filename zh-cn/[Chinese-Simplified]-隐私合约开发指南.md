# PlatON 安全多方计算

## PlatON MPC 简介

[PlatON](zh-cn/[Chinese-Simplified]-快速指南) 平台作为下一代`Trustless`安全数据计算架构，为实现隐私计算提供了基础设施。PlatON公链网络和外部计算网络组合成一个数据为中心的复合网络，为用户提供安全计算服务，实现数据的价值的流动。在 `PlatON`平台上，`MPC`计算虚拟机（`MPC VM`）作为 `PlatON` 计算架构中关键组件，提供了`MPC`计算任务的动态执行环境。

## 架构图

<img src="zh-cn/privacy-contract/images/mpc_structure.png" width = "1000" />   

## MPC 使用场景

**典型案例**：谁是百万富翁？

**案例表述**：两个百万富翁`Alice`和`Bob`想知道他们两个谁更富有，但他们都不想让对方知道自己财富的任何信息。

**解决方案**：使用`MPC`计算，两个富翁可以在不知道对方具体财产的情况下，与对方进行比较，并计算出谁最大（最富有）。

## 隐私合约

隐私合约是PlatON上一种特殊的安全多方计算（MPC）合约，使用`C++`语言进行开发，运行在`PlatON`公链节点的`MPC`计算虚拟机（`MPC VM`）里。隐私合约基于PlatON平台,借助安全多方计算（MPC）的能力，实现了对计算方数据的隐私保护，并提供了安全多方计算服务。

PlatON公链封装了隐私计算运算需要的基础数据类型，并提供这些数据类型的安全运算，用户可以使用这些数据类型，自由组合实现自己的隐私算法。PlatON公链设计并提供了隐私计算安全协议来传输数据和计算结果，参与方计算方在不泄露实际数据前提下，实现隐私计算。

当前版本仅提供两方安全计算的隐私合约编写规范，三方及多方的隐私计算版本将会陆续发布。

* [深入理解隐私合约](zh-cn/privacy-contract/_private_contract_manual)
## MPC任务智能合约 

`MPC任务智能合约` 定义 `MPC` 计算规则，规定了计算的参与者的地址信息、算法信息、计算后的红利如何分配等。
一个 `MPC任务智能合约` 定义的规则不可改变。例如：合约`CC`中定义了计算双方为 `A` 和 `B`，那么该合约只能用于 `A & B` 之间进行计算，其它计算方无法成功调用该合约开启隐私计算。

* [深入理解MPC任务智能合约](zh-cn/privacy-contract/_mpc_part_toc)

## MPC SDK

提供给应用程序用于接入MPC计算环节的软件开发工具包（JAVA SDK），便于快速进行应用程序对接，实现业务逻辑，并将MPC计算任务交互细节透明化，降低隐私应用程序开发门槛。

* [MPC-SDK接入手册](zh-cn/privacy-contract/_mpc_sdk_usage)

## Getting Start 

如何快速的搭建一个MPC计算的开发和运行环境，请参考如下步骤：

* [快速搭建2个MPC计算节点](zh-cn/[Chinese-Simplified]-私有网络)
* [启动测试程序与节点建立连接](zh-cn/privacy-contract/_mpc_sdk_usage#%e6%ad%a5%e9%aa%a45-%e5%90%af%e5%8a%a8%e6%9c%8d%e5%8a%a1)
* [编写算法创建Private Contract](zh-cn/privacy-contract/_mpc_sdk_usage#%e6%ad%a5%e9%aa%a41-%e7%bc%96%e5%86%99%e7%ae%97%e6%b3%95)
* [发起计算并查看结果](zh-cn/privacy-contract/_mpc_sdk_usage#%e6%ad%a5%e9%aa%a46-%e5%8f%91%e8%b5%b7%e8%ae%a1%e7%ae%97)

## Related Resources

* [PlatON MPC计算节点搭建](zh-cn/[Chinese-Simplified]-私有网络#%e4%b8%ba%e8%8a%82%e7%82%b9%e5%90%af%e7%94%a8MPC%e5%8a%9f%e8%83%bd)
* [合约开发手册](zh-cn/[Chinese-Simplified]-Wasm合约开发指南)

## License of This List

