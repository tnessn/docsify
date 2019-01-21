## Wasm Contract Overview

### Brief introduction to Wasm Contracts

The smart contract system used in the `PlatON` platform is completely different from traditional smart contracts. They are _Wasm Contracts_ designed for heavy computing.

PlatON is essentially a `serverless`, distributed service platform under a decentralized topology. The Wasm contracts are FaaS (Functions as a Service) applications deployed on it. Users only need to submit the contract code to the PlatON platform in order to deploy the contracts as services, and then pay for the resources consumed in the process of executing the code.

The Wasm contracts use `C++` syntax. A large class library - the [Wasm contract built-in library](https://pwasmdoc.platon.network/modules.html) \- is provided in order to simplify the process of integrating with the PlatON platform and writing high-performance, powerful smart contracts.

### The PlatON virtual machine

The PlatON virtual machine `pWASM` is the operating environment for the Wasm contracts. Executed in a completely isolated sandbox, the contract logic is not able to access the network, file system, or any other external resource. Even access between Wasm contracts is limited.

This document is mainly an introduction to Wasm contract development guide under Windows. Development guides for other environments will be released very soon, so stay tuned!

## Development environment setup

* [Setting up a Wasm contract development environment](_setting-up-Wasm-Contract-dev-env#setting-up)

## Quick start

* [Write your first Wasm contract](_setting-up-Wasm-Contract-dev-env#write-contract)
* [Deploying contracts and Testing](_setting-up-Wasm-Contract-dev-env#deploy-and-test)

## Common issues

**Unable to deploy contract**

**A** : Please make sure that the energy settings in the configuration file are reasonable and that the account used is unlocked on the node side.

**More questions**

If you have other questions, or if your question cannot be found here, please submit an issue [here](https://github.com/PlatONnetwork/PlatON-Go/issues/new).