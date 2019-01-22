> A open-source graphical desktop wallet client

## What is Samurai

Samurai is PlatON's open-sourced digital wallet desktop client terminal (the client), which can manage basic wallet as well as multi-signature wallet (or Joint wallet). It also provides deployment and operational guides of meta-smart contracts for developers, procedures of PlatON private chain building, voting mechanism of candidate nodes according to PlatON economic model, participation rules of PlatON ecosystem governance etc.

In the future, Samurai will provide more rich functions for different users, and we are dedicated to help user to understand and use the PlatON ecosystem better and deeper.

**Core function are listed as below:**

- Building PlatON Private network (a.k. private chain)

- Wallet creation & importation (supports Keystore, Mnemonic phrase, private key)

- Joint wallet(multi-signature) creation and importation 

- Management of wallet balance and transaction details

- Transaction of PlatON Energon

- Contracts debug test and deployment

- Application for Validator candidate node with staking

- Participating in Validator node voting (To be released)

Btw, Current Samurai is a beta release. This manual intents to help you solving common issue, please feel free to submit any relevant questions at [issues](https://github.com/PlatONnetwork/wiki/issues),  and welcome to join our technical community!


## How to download and install Samurai

At present，Samurai can run on Windows and Linux, please click [here](https://github.com/PlatONnetwork/Samurai/releases) to download. In the future, Samurai will also support running on MacOS system, Please look forward to it. 

+ **Brief installation instruction on Windows:**


```
1.Download Windows installer samurai.exe.
2.Dual-click the installer and follow the Installation wizard.
3.Shortcut of Samurai will be created on the desktop automatically.


```
**Note**:  

*Just follow the Installation wizard for the first time Samurai being  installed, otherwith,  you need to manually delete the folder (Default path: <u>C:\Users\Username\AppData\Roaming\Samurai</u>，and the Username is the account name of your login to the PC. ) where you saved the Blockchain data and as shown in the following figure, and then follow the installation wizard to install.*



![Image text](image/Keystore_address.png)



+ **Brief installation instruction on Linux:**


```
Installation instruction of tar.xz package.
1.Download Linux Samurai package,such as Samurai-Linux64.tar.xz
2.Right click to extract the installation package.
3.Go to the extracted folder and double-click the executable Samurai to open the client.


```
+ **Brief installation instruction on MacOS:**

MacOS is not supported for the time being



## How to use Samurai

### Network

- [How to join in PlatON TestNet](_Join-in-a-Network#join_net)

- [How to create a PlatON Private-net](_Join-in-a-Network#create_private)

### Wallet

- [How to create a wallet](_Classic-Wallet#create_wallet)

- [How to import & restore a wallet](_Classic-Wallet#import_wallet)

- [How to send and receive funds](_Classic-Wallet#send_recv_atp)

- [Why is the test Energon in the wallet cleard](_Classic-Wallet#why_is_cleard)

### Joint Wallet

- [What is Joint wallet](_Joint-Wallet#what_is)
- [How to create a Joint wallet](_Joint-Wallet#how_to_create)

- [How to add a Joint wallet that has been created](_Joint-Wallet#how_to_add)
- [How to send and receive funds with Joint wallet](_Joint-Wallet#how_to_use)

### Transaction

- [How to confirm a transaction](_Confirm-Transactions#comfire_txs)

### Wasm contracts

- [What is wasm contract](_Wasm-Contracts#what_is_msc)

- [How to deploy a contract](_Wasm-Contracts#how_to_deploy)

- [How to add a contract that has been created ](_Wasm-Contracts#how_to_add)

- [How to execute a contract ](_Wasm-Contracts#how_to_run)
### Validator Node
- [What’s candidate node](_Validator-Node#what_is_CN)

- [How to be a validator node](_Validator-Node#how_to_be_VN)

- [How to improve the probability to become validator node](_Validator-Node#how_to_improve)

- [Why are candidate nodes eliminated](_Validator-Node#why_be_eliminated)

- [How to re-apply for validator node if eliminated](_Validator-Node#how_to_re-apply)

- [How to withdraw the application](_Validator-Node#how_to_withdraw)

- [How to redeem the stakes](_Validator-Node#how_to_redeem_stakes)

