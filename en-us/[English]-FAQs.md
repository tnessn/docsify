> Answers to frequently asked questions about PlatON

These FAQs are provided for informational purposes only and should not be relied upon exclusively or considered representations or warranties of any kind. Please refer to the GitHub repository and other resources for more detailed information.

## What is PlatON?

More than a blockchain platform, PlatON introduces a trustless computing architecture to address scalability and privacy issues with advanced encryption algorithms including verifiable computing, homomorphic encryption, secure multiparty computing, and more. PlatON's technology allows the executing of more heavy, data-privacy smart contracts. For more information, please read the PlatON Technical White Paper.

## Which programming language/s do you use?

We use Golang to develop the PlatON. Besides Golang we use Python, Java, C++, and other languages to implement SDKs and applications.

## Is PlatON released under open-source software license?

Yes, PlatON has developed under an open-source LGPL3.0 software license.

## How can I access an PlatON network?

You can access an PlatON network with your private key. The Baleyworld TestNet is now open, you can download the Samurai Wallet client and connect to TestNet. Please note the networks are run by the community independently so please do research by yourself to find the network that you need. Please be mindful of applications requesting your private key - some of them may be scammers wishing to steal your key. Do thorough research before potentially giving out access to your account.

## Can I launch a blockchain myself using the PlatON software?

PlatON is developed under an open-source LGPL3.0 license. Anyone can use the PlatON for free. 

Entrepreneurs interested in building their own blockchain derived from our PlatON Software can fork our repository and customize it for their use.

## Is PlatON designed for the creation of private or public permissioned blockchains?

Currently PlatON cannot be used to create both private and public permissioned blockchains. 

The PlatON Foundation will support community partners to develop enterprise version.

## Where can I download an PlatON wallet? 

 There is a graphical client for basic PlatON wallets (a.k.a.“Samurai”) provided along with the PlatON Software.

There are various community projects that you can refer to which are seeking to create a client with graphical interface for PlatON wallet. However, PlatON does not endorse any of these projects officially, so you need to make your own evaluation of any such offering.

## What is Energon？
Each Dapp running on PlatON needs to consume a certain amount of resources (including computing power, bandwidth, storage, data, etc.). Energon is used to pay for transaction fees that are priced based on resource usage.

## When I connect to TestNet, how do I apply for Energon to test the transfer function?

Developers can visit the PlatON official website to apply for TestNet Energon.

## Are there transaction fees on PlatON?

Yes. In Platon, You need to pay a certain amount of Energon as miner reward for every transaction. 

## What consensus protocol does PlatON use?

PlatON's consensus as known as Giskard combined the PoS and BFT mechanisms.

Energon holders continue to vote through staking to maintain a small list of dynamic consensus node candidates, and randomly select consensus node from the list using VRF and probability distribution.

PlatON adopts a parallel BFT consensus, that is, block production and block validation are performed in parallel, which greatly improves the rate of block production while ensuring BFT 1/3 fault tolerance.

## Will block producers be required to stake in order to be able to verify transactions and create new blocks?

Yes. This is the only way a platform participant can become a block producer candidate.

## Are all energon holders able to cast votes to elect block producers?

Yes. However,  the staking amount of a vote depends on the size of the vote pool and the total amount of Energon.

## What is the difference between smart contract on PlatON and Ethereum?

Unlike Ethereum smart contracts mainly written in solidity, Platon's smart contracts are divided into three categories.

**Wasm Contracts** support high-level language development and compiles to executable Wasm. Transactions that trigger wasm contracts are packaged by the consensus nodes, and the nodes in the entire network repeat the verification. The statuses of the wasm contracts are saved to the public ledger.

**Verifiable Contracts** are developed and published in the same way as wasm contracts; they also compile to executable Wasm. The state transitions of verifiable contracts are asynchronously executed by computation nodes off the chain. After a computation is completed, the new state and the state transition proof are submitted to the chain, and the entire network of nodes can quickly verify the correctness and update the new state to the public ledger. Verifiable contracts support complex, heavy computing logic without affecting the performance of the entire chain.

**Privacy Contracts** also support high-level language development and compile to executable Intermediate Language (LLVM IR). The input data to privacy contracts is stored locally on data nodes. The data nodes perform private computation off the chain using secure multiparty computation, and submit the computation result to the chain.

## Can my data be exchanged through PlatON under the premise of ensuring privacy?

PlatON's privacy contracts can be used for secure transactions of data. Privacy contracts guarantee data privacy and the automatic delivery of data and tokens between two parties in a transaction.

## How can I join the PlatON community？

There are numerous ways to join the PlatON Community:
- Via email support@platon.network
- Via github https://github.com/PlatONnetwork
- Via twitter DM @PlatON_Network
- Via medium https://medium.com/@PlatON_Network
- Via Linkedin https://www.linkedin.com/company/platon-international-limited/
- Via reddit https://www.reddit.com/user/PlatON_Network