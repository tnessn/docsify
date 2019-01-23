> PlatON is more than a blockchain platform. PlatON should be considered as a large-scale computing network, and a trustless computing infrastructure built on cryptographic algorithms.

![architecture](https://raw.githubusercontent.com/wiki/PlatONnetwork/wiki/architecture-en.png)

### Challenges of Blockchain
["The Scalability Trilemma"](https://github.com/ethereum/wiki/wiki/Sharding-FAQs) raises three core issues of blockchain: scalability, decentralization, and security. In addition, with the occurrence of data leak scandals of companies such as Google, Facebook, and Marriott, and the EU General Data Protection Regulation (GDPR) taking effect, privacy issues are increasingly becoming a fourth important issue of blockchains. In fact, when people now talk about large-scale applications of the blockchain the problems of scalability and privacy are at the heart of the discussion.

**Scalability** has been recognized as the biggest problem of blockchain. The current mainstream blockchain processes 3-20 transactions per second, which in terms of processing power is orders of magnitude lower than what is required to run a mainstream financial market. Although the industry is actively implementing various solutions, these are limited by the "The Scalability Trilemma", i.e. premised on the sacrifice of decentralization or security. Blockchain consensus-based strategy also limits smart contracts from supporting complex computational logic.

**Privacy** is another major issue of the blockchain. Although its properties of tamper resistance, decentralization and trustlessness are appealing, the blockchain is also facing the same predicament as Big Data and AI techonology, that is to say not getting data. No company or individual is willing to publish private information to the public ledger, which records can be read freely by the government, family members, colleagues and commercial competitors without restrictions.

### PlatON's Solution
**Verifiable computation.** Given the existing limitations of chain consensus, the function of the chain should be "verification" rather than "computation". More and more people are actively working on implementing solutions that improve the scalability of the blockchain through off-chain strategy. Although the chain has been recognized as a trustless environment, the implementation of off-chain solutions introduces new factors of distrust. PlatON's verifiable computing (VC) cryptography algorithm takes the trust offline. Through verifiable computation, contracts only need to be computed once off the chain. All nodes can then quickly verify the correctness of the computation, which improves transaction processing performance, and enables PlatON to support trustless computation of complex contracts.

**Privacy-preserving computation.** PlatON implements complete privacy-preserving computation by superimposing homomorphic encryption (HE) and secure multiparty computation (MPC) to ensure the privacy of the input data and the computational logic itself. Trustless computing on PlatON relies only on falsifiable cryptographic assumptions, compared to trusted integrity computation that relies on trusted hardware or TEE (such as SGX) provided by third-party manufacturers, thus providing unprecedented private data security with no trust boundaries during its life cycle.

## PlatON Basic Concepts
### Nodes
![nodes](https://raw.githubusercontent.com/wiki/PlatONnetwork/wiki/nodes-en.png)

PlatON decouples transaction execution from blockchain consensus and builds a scalable trustless computing network off the chain. Therefore, the nodes in PlatON mainly are of the following categories:

**Light Nodes** do not save the data of all blocks, only block header information and the data related to itself, and relies on Full Nodes for fast transaction verification. Light nodes participate in transactions and broadcasts of block data across the entire network.

**Full Nodes** save the data of all blocks, and can directly verify the validity of transaction data locally. Full nodes participate in transactions and broadcasts of block data across the entire network.

**Block Producers** are responsible for executing transactions and package transaction data into blocks. In the Giskard consensus protocol, block producers are randomly generated based on VRF and probability distribution, and consensus is reached through the asynchronous BFT protocol.

**Computing Nodes** are introduced into the blockchain ecosystem by PlatON. Computational nodes are the foundation of the trustless computing network. They mainly provide computing power, executing complex contracts off the chain and using VC algorithms to generate computation proofs for fast verification on the chain.

**Data Nodes** are another important component of a trustless computing network. Based on homomorphic encryption (HE) and secure multi-party computation (MPC), data nodes can input local data into the computing network while guaranteeing privacy.

### Accounts
Compared to the account model, UTXO does not support smart contracts, and many DAG projects are actively exploring smart contracts, but there are no mature and stable solutions, so PlatON chooses the mature and stable account model which supports smart contracts. In PlatON, each account has a state and a 20-byte address associated with it. Accounts are divided into two categories:

**Basic Accounts** are controlled by a private key, which can be generated by the user through the wallet client or the command line. In PlatON, basic accounts can create transactions and sign them with the private key. In addition to basic accounts storing account balance, a smart contract can be authorized to extend and access custom attributes.

**Contract Accounts** are controlled by code and do not have private keys. Contract accounts addresses are generated when the contracts are deployed. Unlike basic accounts, contract accounts cannot initiate new transactions on their own. Whenever a contract account receives a message, the code inside the contract is activated, allowing it to read and write to internal storage, and to send other messages or create contracts.

### Smart Contracts
From a technical point of view, the PlatON computing network is essentially a decentralized FaaS platform. Correspondingly, smart contracts can be thought of as FaaS functions. The smart contracts in PlatON fall into three categories.
![contracts](https://raw.githubusercontent.com/wiki/PlatONnetwork/wiki/contracts-en.png)

**Wasm Contracts** support high-level language development and compiles to executable Wasm. Transactions that trigger wasm contracts are packaged by the consensus nodes, and the nodes in the entire network repeat the verification. The statuses of the wasm contracts are saved to the public ledger.

**Verifiable Contracts** are developed and published in the same way as wasm contracts; they also compile to executable Wasm. The state transitions of verifiable contracts are asynchronously executed by computation nodes off the chain. After a computation is completed, the new state and the state transition proof are submitted to the chain, and the entire network of nodes can quickly verify the correctness and update the new state to the public ledger. Verifiable contracts support complex, heavy computing logic without affecting the performance of the entire chain.

**Privacy Contracts** also support high-level language development and compile to executable Intermediate Language (LLVM IR). The input data to privacy contracts is stored locally on data nodes. The data nodes perform private computation off the chain using secure multiparty computation, and submit the computation result to the chain.

## Consensus in PlatON
In blockchain, decentralization of block production can be quantified as the number of block producers. Scalability can be quantified as the number of transactions per unit of time that the system can process. Safety can be quantified as the cost of mounting a Byzantine attack that affects liveness or transaction ordering. Considering the trade-offs between these quantitative indicators, PlatON's Giskard consensus finally decided on combining the PoS and BFT mechanisms.

**PPoS (Probabilistic PoS)**, in fact, all PoS systems have trade-offs between the number of consensus nodes and performance. DPoS is biased towards fewer consensus nodes in exchange for higher performance. Algorand uses a random method to select consensus nodes across the network, but can only run on strongly synchronous networks. These are two typical extreme methods. PlatON adopts a compromise approach. Any Energon holder can participate in the consensus node through staking. Other Energon holders continue to vote through staking to maintain a small list of dynamic consensus node candidates, and randomly select candidates from the list using VRF and probability distribution. This method narrows the selection of consensus nodes and effectively avoids the problem of over-centralization.

**CBFT (Parallel BFT)**. Currently the various commonly used xBFTs use synchronous processing, that is, the next block is produced after confirming a block. This method has a performance upper limit. PlatON adopts a parallel BFT consensus, that is, block production and block validation are performed in parallel, which greatly improves the rate of block production while ensuring BFT 1/3 fault tolerance.

## PlatON Computational Ecosystem
Computing power and data are necessary for computation. PlatON naturally forms a computing power and data trading market, constituting a complete computing ecology.

### Computing Power Market
PlatON's verifiable contract system is a naturally secure computing power market with automatic clearing and settlement. Complex computing tasks can be published through verifiable contracts, which are processed by numerous off-chain computing nodes with high computing power that compute and release computation proofs. After computation correctness is quickly verified, computational fees are immediately cleared and settled.

### Data Market
We are in the era of AI driven by Big Data. People are becoming increasingly aware of the value of data, but the data is scattered across different organizations, and the data dimensions of each organization are limited, so it is difficult to fully exploit the value of the data. In order to unite it, it is necessary to give your data to another party, or both parties will give the data to a common intermediate platform, but protecting the data from being leaked to malicious parties - private user data in particular - is a big concern. PlatON's privacy contracts can be used for secure transactions of data. Privacy contracts guarantee data privacy of data nodes. Smart contracts are also an automated clearing and settlement institution, which guarantee the automatic delivery of data and tokens between two parties in a transaction.