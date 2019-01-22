> PlatON开源的桌面版钱包客户端

## 什么是Samurai

**Samurai**是PlatON开源的图形化桌面版钱包客户端，支持基础的普通钱包管理，基于多重签名技术的联名钱包管理，同时为开发者提供Wasm合约的部署与运行，PlatON私用链的可视化搭建，以及基于PlatON经济模型下的候选节点竞选申请与投票，参与PlatON生态治理等功能。未来，Samurai将持续为不同用户提供更加基础、更加丰富的应用功能，让大家更好的了解和使用PlatON生态服务。

Samurai核心功能如下：

- 搭建PlatON私用网络
- 创建钱包/导入钱包（支持使用Keystore，助记词，私钥）
- 支持联名钱包（基于多重签名技术）
- 查看钱包余额及交易明细
- 发送及提取Energon
- 合约部署与测试运行
- 候选节点竞选申请与质押管理
- 参与候选节点投票(敬请期待)

请注意，Samurai仍处于测试阶段，可能会出现一些问题。 本指南将帮助您解决一些最常见的问题。如有更多问题欢迎提交到[issues](https://github.com/PlatONnetwork/wiki/issues) ！


## 如何下载安装PlatON Samurai客户端

Samurai目前适用于Windows 及Linux系统，下载请点击[这里](https://github.com/PlatONnetwork/Samurai/releases)。未来Samurai还会支持在MacOS系统上运行，敬请期待。

### Windows安装

Windows安装程序下载后，双击安装程序并按照说明进行操作。安装程序会在桌面上添加Samurai的快捷方式。

*注意：由于当前测试阶段，底层版本更新比较大，非初次安装，请您先卸载完后再手动删除如下图所示的保存区块数据的文件夹（默认路径：C:\Users\Username\AppData\Roaming\Samurai，其中Username为您登陆PC的账号名称），然后再按照安装引导进行安装。*



![Image text](image/Keystore_address-cn.png)


### Linux安装

tar.xz源代码包安装说明：

1.下载Linux版Samurai安装包，如：Samurai-Linux64.tar.xz

2.右键解压安装包

3.进入解压的文件夹，双击可执行文件Samurai即可打开客户端

## 如何使用Samurai

### 网络初始化

- [怎么加入PlatON测试网络](_网络初始化#join_net)

- [如何创建PlatON本地私用网络](_网络初始化#create_private)

### 钱包

- [如何创建一个钱包](_钱包#create_wallet)

- [如何导入/恢复一个已有的钱包](_钱包#import_wallet)

- [如何发送、接收Energon](_钱包#send_recv_energon)

- [为什么钱包中的测试币被清零了](_钱包#why_is_cleard)

### 联名钱包

- [什么是联名钱包](_联名钱包#what_is)

- [如何创建一个联名钱包](_联名钱包#how_to_create)

- [如何添加已创建的联名钱包](_联名钱包#how_to_add)

- [如何使用联名钱包发送、接收Energon](_联名钱包#how_to_use)

### 交易

- [如何确认交易](_交易#comfire_txs)

### Wasm合约

- [Wasm合约是什么](_Wasm合约#what_is_msc)

- [如何部署一个Wasm合约](_Wasm合约#how_to_deploy)

- [如何添加别人已部署的Wasm合约](_Wasm合约#how_to_add)

- [怎样运行wasm合约](_Wasm合约#how_to_run)

### 应用-竞选节点
- [什么是候选节点](_竞选节点#what_is_CN)
- [怎样参与候选节点竞选](_竞选节点#how_to_be_VN)
- [如何提高成为验证人概率](_竞选节点#how_to_improve)
- [候选节点为什么会被淘汰](_竞选节点#why_be_eliminated)
- [被淘汰的节点如何再次加入竞选](_竞选节点#how_to_re-apply)
- [如何退出竞选](_竞选节点#how_to_withdraw)
- [如何提取被质押的资产](_竞选节点#how_to_redeem_stakes)
