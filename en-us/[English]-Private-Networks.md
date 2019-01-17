
Sometimes you might not need to connect to the live public network, you can instead choose to create your own private testnet. This is very useful if you don't need to test external contracts and want just to test the technology, because you won't have to compete with other minters and will easily generate a lot of test energon play around.

We assume you are able to install `platon` following the [Installation Instructions](en-us/[English]-Installation-Instructions).

We assumes that the working directory is `~/platon-node` on Ubuntu and is `D:\platon-node` on Windows. Please ensure that the platon program generated in Installation Instructions is copied to the working directory. 

Please note: the following operations are performed in the working directory.

## Single node environment

1. **Download the public and private key pair generation tool `ethkey` for the corresponding platform [here](https://download.platon.network/ethkey-windows-amd64.exe).**



- Modify the file name after downloading to `D:\platon-node` through the browser on Windows


```
D:\platon-node> move ethkey-windows-amd64.exe ethkey.exe


```



- Ubuntu command line


```
$ wget https://download.platon.network/ethkey-linux-amd64
$ mv ethkey-linux-amd64 ethkey


```

2. **Run the public and private key pair generation tool `ethkey` to generate a node ID and a node private key.**



- Windows command line:


```
D:\platon-node> ethkey.exe genkeypair
Address : 0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey: 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127


```



- Ubuntu command line:


```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address : 0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey: 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127


```
PublicKey is the ***node ID***, and PrivateKey its corresponding ***node private key***.

3. **Generate a node coinbase account. For testing, you can pre-fund the account in the genesis block.**



- Windows command line:


```
D:\platon-node> platon.exe --datadir .\data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}


```



- Ubuntu command line:


```
$ ./platon --datadir ./data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}


```
Be sure to note the generated **Address**.

4. **Generate the genesis configuration file.**

Download `platon.json` from [here](https://download.platon.network/platon.json) to the working directory. Change `your-node-pubkey` into the previously generated node ID to make the local common nodes participate in the consensus. Change `your-account-address` into the account generated in step 3. The contents of platon.json are as follows:


```
{
    "config": {
    "chainId": 100,
    "homesteadBlock": 1,
    "eip150Block": 2,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 3,
    "eip158Block": 3,
    "byzantiumBlock": 4,
    "cbft": {
      "initialNodes": ["enode://your-node-pubkey@127.0.0.1:16789"]
    }
  },
  "nonce": "0x0",
  "timestamp": "0x5bc94a8a",
  "extraData": "0x00000000000000000000000000000000000000000000000000000000000000007a9ff113afc63a33d11de571a679f914983a085d1e08972dcb449a02319c1661b931b1962bce02dfc6583885512702952b57bba0e307d4ad66668c5fc48a45dfeed85a7e41f0bdee047063066eae02910000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x99947b760",
  "difficulty": "0x1",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "your-account-address": {
      "balance": "999000000000000000000"
    }
  },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}


```

5. **Configure the private key file of the node.**

Please note: the echo command line argument is the node private key and needs to be replaced with the node private key generated in step 2.



- Windows command line:


```
D:\platon-node> mkdir .\data\platon
D:\platon-node> echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > .\data\platon\nodekey
D:\platon-node> type .\data\platon\nodekey


```



- Ubuntu command line:


```
$ pwd
/home/platon/platon-node
$ mkdir -p ./data/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data/platon/nodekey
$ cat ./data/platon/nodekey


```

6. **Execute the following command to initialize the genesis state.**



- Windows command line:


```
D:\platon-node> platon.exe --datadir .\data init platon.json


```



- Ubuntu command line:


```
$ ./platon --datadir ./data init platon.json


```
The following prompt indicates that a genesis node was successfully created:


```
Successfully wrote genesis state


```

7. **Start nodes**



- Windows command line:


```
D:\platon-node> platon.exe --identity "platon" --datadir .\data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin, personal" --rpc --nodiscover --nodekey ".\data\platon\nodekey"


```



- Ubuntu command line:


```
$ ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey"


```

***Prompt:***

| Option | Description |
| :------------ | :------------ |
| --identity | Specify network name |
| --datadir | Specify the data directory path |
| --rpcaddr | Specify RPC server address |
| --rpcport | Specify RPC protocol communication port |
| --rpcapi | Specify name of the node's RPC API |
| --rpc | Specify HTTP-RPC communication method |
| --nodiscover | Do not enable node discovery |
| --nodekey | Specify node private key file save path |

At this time, log records containing the words "blockNumber" and "growth" appearing in the standard output **indicate that consensus was reached, the chain started successfully, and that blocks are being successfully created**.

We have now built a Platon private network containing a single node with the network name `platon` and a network `ID` of `100`.
You can perform any operation on it, just like on a single node in a public network.

8. **Running in the background**

So far we have let the `platon` process run in the foreground, which locks the terminal, and if we quit the terminal the program will exit.
Background running is not supported on Windows.
Now start the program with nohup on Ubuntu:


```
$ nohup ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey" &


```
This way the process will keep running in the background even if you exit the terminal window.


## PlatON cluster
A `PlatON Cluster` is a network consisting of multiple nodes. At this point we assume that you already know how to build a single Platon node. We will now build a network consisting of two nodes; a network of more nodes will follow a similar workflow.

In order to run multiple `platon` nodes locally, you must ensure that:


- Each node instance has a separate data directory (--datadir)


- Each instance runs on a different port, both `platon` and rpc (--port and --rpcport )


- Each node must know about the other


- The IPC port must be restricted or unique

1. **Create two data directories called data0 and data1 in platon-node directory and two new coinbase accounts for each of the two nodes.**



- Windows command line:


```
D:\platon-node> mkdir data0
D:\platon-node> platon.exe --datadir .\data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

D:\platon-node> mkdir data1
D:\platon-node> platon.exe --datadir .\data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}


```



- Ubuntu command line:


```
$ mkdir -p data0
$ ./platon --datadir ./data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

$ mkdir -p data1
$ ./platon --datadir ./data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}


```
2. **Run`ethkey` to generate a node ID and a node private key for each of the two nodes.**



- Windows command line:


```
D:\platon-node> ethkey.exe genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey :  8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

D:\platon-node> ethkey.exe genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06


```



- Ubuntu command line:


```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06


```
PublicKey is the ***node ID***, and PrivateKey its corresponding ***node private key***.

3. **Modify the genesis configuration file platon.json.**

Add the node information of the two nodes to the **initialNodes** array. Since we are generating a cluster consisting of two nodes, the array length will be two. 

Modify the platon.json file:


- Replace `node0-pubkey` with the ***node ID*** of node 0 generated in step 2.


- Replace `node1-pubkey` with the ***node ID*** of node 1 generated in step 2.


- Replace `node0-account-address` with the ***Address*** of node 0 generated in step 1.


- Replace `node1-account-address` with the ***Address*** of node 1 generated in step 1.


```
……
  "cbft": {
  "initialNodes": [
    "enode://node0-pubkey@127.0.0.1:16789",
    "enode://node1-pubkey@127.0.0.1:16790"]
  }
……
  "alloc": {
    "node0-account-address": {
      "balance": "999000000000000000000"
    },
    "node1-account-address": {
      "balance": "999000000000000000000"
    }
  },
……


```

4. **Create a file called `nodekey` for each node and save the node's private key to it.**

Please note: the echo command line argument is the node private key and needs to be replaced with the node private key generated in step 2.



- Windows command line:


```
D:\platon-node> mkdir .\data0\platon
D:\platon-node> echo 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36 > .\data0\platon\nodekey
D:\platon-node> type .\data0\platon\nodekey

D:\platon-node> mkdir .\data1\platon
D:\platon-node> echo 9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320> .\data1\platon\nodekey
D:\platon-node> type .\data1\platon\nodekey


```



- Ubuntu command line:


```
$ mkdir -p ./data0/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data0/platon/nodekey
$ cat ./data0/platon/nodekey

$ mkdir -p ./data1/platon
$ echo "9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320" > ./data1/platon/nodekey
$ cat ./data1/platon/nodekey


```

5. **Initialize the genesis information for node 0 and start the node.**



- Windows command line:


```
D:\platon-node> platon.exe --datadir .\data0 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data0\platon\nodekey"


```



- Ubuntu command line:


```
$ ./platon --datadir ./data0 init platon.json
$ ./platon --identity "platon" --datadir ./data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data0/platon/nodekey"


```

6. **Initialize the genesis information for node 1 and start the node.**



- Windows command line:


```
D:\platon-node> platon.exe --datadir .\data1 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --ipcdisable --nodekey ".\data1\platon\nodekey"


```
Please note: all nodes except the first node must be started with --ipcdisable option on Windows.



- Ubuntu command line:


```
$ ./platon --datadir ./data1 init platon.json
$ ./platon --identity "platon" --datadir ./data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data1/platon/nodekey"


```

7. **The cluster is set up successfully when both nodes indicate that consensus was reached and that blocks are being inserted into the chain.**