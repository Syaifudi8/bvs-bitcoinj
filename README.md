### What is bitcoinj-multichain?

This is an experimental patched version of BitcoinJ which allows BitcoinJ clients to access a MultiChain private blockchain.  The MultiChain network must be configured to behave like the public Bitcoin network.

In general, a Bitcoin client should:
- Disable target verification
- Disable checkpoints
- Remove any hard-coded IP addresses of Bitcoin nodes
- Remove any hard-coded data related to the Bitcoin Genesis block

With BitcoinJ, the following changes have been made:
- Disable max target verification on block headers
- Disable checkpoints
- Added handling of 'getaddr' messages to avoid disconnection
- Added subclass of MainNetParams called MultiChainParams which clients should use.  This subclass will:
  - Create the MultiChain genesis block to replace the Bitcoin genesis block which BitcoinJ creates internally.
  - Remove any DNS seeds
  - Remove any hard-coded node addresses

The WalletTemplate has been modified so it can connect to MultiChain over localhost or remotely.  Instead of creating MainNetParams, we now create a MultiChainParams object with data from the MultiChain network passed in via environment variables.


#### Build bitcoinj-multichain
```
	mvn clean install -DskipTests
```

#### Build wallet demo
```
	cd wallettemplate
	mvn clean package -DskipTests
```

#### Run wallet demo

Note you should set environment variables first (see below) before launching the wallet.
```
	java -jar target/wallettemplate-shaded.jar -Dorg.slf4j.simpleLogger.defaultLogLevel=debug 
```

Wallet files will be created in the local directory.

#### Set environment variables for wallet demo

Before running the wallettemplate demo, set the following environment variables:
```
BITCOINJ_MULTICHAIN_DEMO_BLOCKHASH=...
BITCOINJ_MULTICHAIN_DEMO_RAWHEX=...
BITCOINJ_MULTICHAIN_DEMO_IP=127.0.0.1
```

You can find the genesis block hash in params.dat or by calling:
```
	multichain-cli NETWORKNAME getblockhash 0
```

You can get the raw hex string of the genesis block by calling:
```
	multichain-cli NETWORKNAME getrawtransaction GENESISBLOCKHASH false
```

#### Create a MultiChain network with Bitcoin behaviour

There is an example Multichain params.dat file in the repository:
```
	multichain_bitcoin.params.dat
```

You can use this file to help create a MultiChain network which behaves like the Bitcoin network:
```
	multichain-util create bitcoin
	cp multichain_bitcoin.params.dat ~/.multichain/bitcoin/params.dat
```

You can change how often blocks are created by editing the file and adjusting the parameters:
- target-block-time
- pow-minimum-bits




### Original BitcoinJ README starts here:


[![Build Status](https://travis-ci.org/bitcoinj/bitcoinj.png?branch=master)](https://travis-ci.org/bitcoinj/bitcoinj)   [![Coverage Status](https://coveralls.io/repos/bitcoinj/bitcoinj/badge.png?branch=master)](https://coveralls.io/r/bitcoinj/bitcoinj?branch=master) 

[![Visit our IRC channel](https://kiwiirc.com/buttons/irc.freenode.net/bitcoinj.png)](https://kiwiirc.com/client/irc.freenode.net/bitcoinj)

### Welcome to bitcoinj

The bitcoinj library is a Java implementation of the Bitcoin protocol, which allows it to maintain a wallet and send/receive transactions without needing a local copy of Bitcoin Core. It comes with full documentation and some example apps showing how to use it.

### Technologies

* Java 6 for the core modules, Java 8 for everything else
* [Maven 3+](http://maven.apache.org) - for building the project
* [Orchid](https://github.com/subgraph/Orchid) - for secure communications over [TOR](https://www.torproject.org)
* [Google Protocol Buffers](https://code.google.com/p/protobuf/) - for use with serialization and hardware communications

### Getting started

To get started, it is best to have the latest JDK and Maven installed. The HEAD of the `master` branch contains the latest development code and various production releases are provided on feature branches.

#### Building from the command line

To perform a full build use
```
mvn clean package
```
You can also run
```
mvn site:site
```
to generate a website with useful information like JavaDocs.

The outputs are under the `target` directory.

#### Building from an IDE

Alternatively, just import the project using your IDE. [IntelliJ](http://www.jetbrains.com/idea/download/) has Maven integration built-in and has a free Community Edition. Simply use `File | Import Project` and locate the `pom.xml` in the root of the cloned project source tree.

### Example applications

These are found in the `examples` module.

#### Forwarding service

This will download the block chain and eventually print a Bitcoin address that it has generated.

If you send coins to that address, it will forward them on to the address you specified.

```
  cd examples
  mvn exec:java -Dexec.mainClass=org.bitcoinj.examples.ForwardingService -Dexec.args="<insert a bitcoin address here>"
```

Note that this example app *does not use checkpointing*, so the initial chain sync will be pretty slow. You can make an app that starts up and does the initial sync much faster by including a checkpoints file; see the documentation for
more info on this technique.

### Where next?

Now you are ready to [follow the tutorial](https://bitcoinj.github.io/getting-started).
