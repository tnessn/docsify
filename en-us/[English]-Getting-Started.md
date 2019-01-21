
> Quickstart: Installing and running PlatON nodes

## Installing PlatON
PlatON supports various Linux flavors as well as Windows and Docker. For each platform installation instructions, please refer to [Installation Instructions]([English]-installation-instructions).

We assumes that the working directory is `~/platon-node` on Ubuntu and is `D:\platon-node` on Windows. Note that the following operations are performed in the working directory.

## Connecting to the network

### Connect to the Baleyworld testnet

The Baleyworld TestNet is live. There are two ways to connect to it:
* download the [Samurai Wallet](https://download.platon.network/0.3/samurai-windows-x86_64-0.3.0.zip) client and follow the [instructions here]([English]-Samurai-Wallet) to install and connect to TestNet.
* Use interactive command line tools to install and connect [learn more](https://github.com/PlatONnetwork/wiki/wiki/_javascript-console)

### Building a private network
If you are unable to connect to external networks, or if you want to test Platon locally, see [Creating private networks]([English]-private-networks).

### Connecting to nodes

The `platon` console can be accessed over HTTP:
- Windows command line
```
D:\platon-node> platon.exe attach http://localhost:6789
```

- Linux command line
```
$ ./platon attach http://localhost:6789
```

## Creating PlatON accounts
For testing, we can easily create multiple accounts in the `platon` console.
```
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0x3dea985c48e82ce4023263dbb380fc5ce9de95fd"
```
The output is the account address.

### listing accounts
```
> eth.accounts
["0x566c274db7ac6d38da2b075b4ae41f4a5c481d21", "0x3dea985c48e82ce4023263dbb380fc5ce9de95fd"]
```
In this example, `0x566c274db7ac6d38da2b075b4ae41f4a5c481d21` is the coinbase account created when [creating private network]([English]-private-networks) and has been pre-allocated in the genesis block.

## unlocking account
You need to unlock your account before sending a transaction.
```
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x566c274db7ac6d38da2b075b4ae41f4a5c481d21
Passphrase: 
true
```
Enter the password you set when you created your account and you can successfully unlock your account.

## Sending transactions
The following command is to transfer from account `0x566c274db7ac6d38da2b075b4ae41f4a5c481d21` to account `0x3dea985c48e82ce4023263dbb380fc5ce9de95fd`.

It can be replaced with your actual account address. Note that the 0x prefix must be added.

It must be ensured that there is sufficient balance in the transfer account. If you connect to testnet, you can apply Energons for testing on the PlatON official website. If you build your own private network, you can pre-allocate Energon in the genesis block.

```
> eth.sendTransaction({from:"0x566c274db7ac6d38da2b075b4ae41f4a5c481d21",to:"0x3dea985c48e82ce4023263dbb380fc5ce9de95fd",value:10,gas:88888,gasPrice:3333})
"0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b"
```

The output is the transaction hash, the following command can query the transaction details.
```
> eth.getTransaction("0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b")
{
  blockHash: "0xf5d3da50065ce5793c9571a031ad6fe5f1af326a3c4fb7ce16458f4d909c1613",
  blockNumber: 33,
  from: "0x566c274db7ac6d38da2b075b4ae41f4a5c481d21",
  gas: 88888,
  gasPrice: 3333,
  hash: "0xa8a79933511158c2513ae3378ba780bf9bda9a12e455a7c55045469a6b856c1b",
  input: "0x",
  nonce: 0,
  r: "0x433fe5845391b6da3d8aa0d2b53674e09fb6126f0070a600686809b57e4ef77d",
  s: "0x6b0086fb76c46024f849141074a5bc79c49d5f9a658fd0fedbbe354889c34d8d",
  to: "0x3dea985c48e82ce4023263dbb380fc5ce9de95fd",
  transactionIndex: 0,
  v: "0x1b",
  value: 10
}
```

### Viewing account balance
```
> eth.getBalance("0x566c274db7ac6d38da2b075b4ae41f4a5c481d21")
998999999999999999990
```
