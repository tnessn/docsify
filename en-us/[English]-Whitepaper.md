# A High-Efficiency Trustless Computing Network

> An introductory paper to PlatON.

## Summary

The scalability issue of blockchain is intrinsic to the consensus algorithm that it relies on to validate transaction or computation in a trustless way. The more nodes participate in replicating a single computation, the more confidence is bestowed on the correctness of the results. However, the time to consensus and resources committed on the work increase drastically with the number of nodes prohibiting blockchain being adopted in a large scale. The flipside is consensus is not the essence of blockchain’s appeal but the trustless feature which can be attained cryptographically. 

Verifiable computation (VC) is originally a scheme enabling a client or verifier to verify the correctness of the results computed by an untrusted cloud provider. The computing party is responsible to conjure a cryptographic proof for the verifier to easily evaluate the correctness of the output. Compared to consensus algorithm, VC, in theory, guarantees correctness without replicated computation, tremendously reduces the time and cost otherwise consumed in running thousands of nodes and synchronizing them for block production. In most circumstances, VC can potentially be the more efficient infrastructure for smart contract execution and diminish the role of blockchain to a tamper-free database for the storage and public access of proofs. 

PlatON uses VC to achieve scalability, verifiability and privacy in trustless computing. The proprietary technology developed by the team in the past few years further reduces the overhead time and resources for the prover by several magnitude turning it into a more practical solution for general trustless computing. Moreover, as privacy is concerned in many trustless computing cases, secure multi-party computation (MPC) and homomorphic encryption (HE) are employed to prevent exposure throughout the entire lifetime of the data. The goal of PlatON is to construct a full stack infrastructure for decentralized apps, while individual module such as VC will be available to connect other blockchains to PlatON for scalable and privacy-preserving computing.

For more detailed information, please refer to the complete [white paper](https://www.platon.network/static/pdf/en/PlatON_A%20High-Efficiency%20Trustless%20Computing%20Network_Whitepaper_EN.pdf).