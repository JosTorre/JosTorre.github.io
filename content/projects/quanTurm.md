---
title: "QuanTurm"
date: 2022-03-31T17:08:40+02:00
draft: false
---

`CREATED BY JOSE TORRE`

[QuanTurm](https://github.com/JosTorre/kademlia) (QT) is a prototype of a post-quantum secure blockchain that runs over Kademlia's distributed hash table peer to peer network. 

### Index

| [Intro](#intro) |
[Design](#design) |
[How it works](#how-does-it-work) |
[Installation](#installation) |
[Use it!](#how-to-use-it) |
[Notes](#additional-notes) |
[License](#license) |

*Special thanks to Jürgen Fuß and Mattia V. for their feedback on my work.*

### Intro

With the recent invention of quantum computers and their computing capacities, many possibilities for development arise as well as threats if their power were to be used for evil purposes. Yet if the possibility of quantum attacks on classical cryptographic mechanisms has not been normalized, the idea of *'hack today, crack tomorrow'* makes it necessary to start developing post-quantum resistant systems.

One of the most meaningful digital inventions from the last years is Blockchain. Based on the Bitcoin paper, a new almost immutable decentralized data structure was proposed. Nevertheless, there are some flaws in its design that make it unscalable and weak to attacks made with a strong computational power.

Some solutions have been proposed in order to make blockchains more performant or more secure, but most of them lack one of the three main characteristics: security, decentralization or scalability. New improved versions of blockchain try to improve in these characteristics but it is hard to comply with the three of them.

Adding the previously mentioned quantum threats, there is also the need of developing a post-quantum secure blockchain.

Nonetheless, the recipe for making a post-quantum secure blockchain is __not__ as easy as:

*Blockchain + Post-Quantum Cryptography = Post-Quantum Blockchain.*

Why not?

Firstly, because blockchain itself as a distributed ledger, has already scalability issues, each peer needs to download, save and update the whole ledger by itself, and the mining process consumes high amounts of energy and needs great computational power.
Secondly, the new post-quantum secure signing mechanisms require more processing power and memory. 

Therefore, simply replacing classical cryptographic mechanisms in a blockchain with post-quantum ones is not sufficient to properly design a post-quantum secure blockchain.

### Design

The first part of my project was to make a post-quantum version of [LightChain](https://github.com/yhassanzadeh13/lightchain-container), as it offered quick information saving and retrieval times and a distributed storage on a peer to peer network without any mining process. Un/fortunately LightChain didn't work as described on the papers. The project on Github wasn't finished, and that made me develop my own version of a post-quantum secure blockchain, taking some concepts of LightChain into my prototype.

For starters, post-quantum secure signature mechanisms were researched. From the signature mechanisms Lattice based approaches jumped off the page. Therefore for this project the main digital signature mechanism chosen was Falcon. Falcon offers quick verification times, small signatures (compactness), and security. Even though Falcon offers compactness compared to other post-quantum signature mechanisms, signature size is biger than signatures that classical signature mechanisms produce.

Next, in order to countermeasure the data sizes produced by post-quantum keys and signatures and therefore the size of transactions and blocks, a distributed storage solution for peer to peer network was chosen. For this project, [Kademlia](https://kademlia.readthedocs.io/en/latest/) was selected to be the P2P network with distributed storage on top of which the blockchain was developed. 
Kademlia posed to be a great solution since it is well documented, offers a fault-prone environment, and very quick times of information saving and querying in the network between other advantages.

Thirdly, transactions and blocks are saved separately in the network. Transactions are referenced in each block, making the whole blockchain lighter.

Finally, instead of having a mining process, the concept of PoV (Proof of Validation) is taken from LightChain, where N number of validators are randomly chosen based on the hash of either block or transaction. The validator earns a commission per transaction or block validation.

All this is just a prototype and is going to be improved. The code's concept is considered to be secure in a DApp scenario, where the code cannot be altered.

### How does it work?

QuanTurm is coded in a class structured manner. The following classes are the backbone of this project:

- __qT Blockchain:__ This class dictates how the whole blockchain works; the transaction and block creation and verification.
- __Network:__ This class is the heighest level of each peer as a server. It provides the set() and get() methods to save and query data.
- __Node:__ A lower level of the server, which contains the ID and IP address with port of each node.
- __Storage:__ The memory module of each peer in the network with two main functions: save and obtain data. 
- __Protocol:__ RPC makes possible to access services and resources from other nodes on demand. In this case the services are storage or blockchain related.
- __Routing:__ For communication between peers.
- __Crawling:__ Module to obtain data about new neighbours or fetch hashed values.

*Each peer can play three roles in the blockchain: Payer, Receiver and Verifier.*

The roles defined in this newly designed QuanTurm blockchain operate as follows:

1. Servers are created.
2. Nodes are started and bootstrapped (network is formed).
3. Each peer generates its signature keys.
4. First peer creates the Genesis Block and inserts it into the network.
5. Transactions are made.
    1. Receiver creates a transaction.
    2. Receiver sends the transaction to the payer.
    3. Payer approves the transaction by signing it, and sends it back.
    4. Receiver signs the transaction.
    5. Receiver sends the transaction to a random node to verify.
    6. Verifier node verifies the transaction and sends it back.
    7. Receiver saves the transaction to the network and saves the transaction id.
6. If the max number of transactions per block is reached by a Receiver, it creates a block.
7. Receiver asks for block verification, and sends the created block to a random verifier.
8. Verifier verifies the block and sends it back.
9. Receiver inserts the verified block into the network.

### Installation

In order to use QuanTurm, there are three things you have to install/download.

1. #### OQS Library

For the exact instructions on how to install the python module wrapper for the liboqs C Library please visit the [github repo](https://github.com/open-quantum-safe/liboqs-python).
Otherwise, here are the most important details:

First, you must build liboqs according to
the [liboqs building instructions](https://github.com/open-quantum-safe/liboqs#linuxmacos)
with shared library support enabled (add `-DBUILD_SHARED_LIBS=ON` to the `cmake` command), followed (optionally) by
a `sudo ninja install`
to ensure that the shared library is visible system-wide (by default it installs under `/usr/local/include`
and `/usr/local/lib` on Linux/macOS).

On Linux/macOS you may need to set the `LD_LIBRARY_PATH` (`DYLD_LIBRARY_PATH` on macOS) environment variable to point to
the path to liboqs' library directory, e.g.

    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib

assuming `liboqs.so.*` were installed in `/usr/local/lib` (true if you ran `sudo ninja install` after building liboqs).

On Windows ensure that the liboqs shared library `oqs.dll` is visible system-wide. Use the "Edit the system environment
variables" Control Panel tool or type in a Command Prompt

	set PATH="%PATH%;C:\some\dir\liboqs\build\bin"

of course replacing the paths with the ones corresponding to your system.

liboqs-python does not depend on any other Python packages. The package isn't hosted on PyPI yet. We recommend to
install it into a virtualenv using:

	# create & activate virtual environment, e.g.:
	python3 -m venv <virtualenv_name>
	source <virtualenv_name>/bin/activate
	
	cd /some/dir/liboqs-python
	python3 setup.py install

On Windows replace the command `source <virtualenv_name>/bin/activate` with `<virtualenv_name>\Scripts\activate.bat`.

##### Testing OQS Library

The liboqs-python project should be in the `PYTHONPATH`:

	export PYTHONPATH=/some/dir/liboqs-python

or, on Windows platforms, use the "Edit the system environment variables" Control Panel tool or type in a Command Prompt

    set PYTHONPATH="C:\some\dir\liboqs-python"

As any python module, liboqs wrapper components can be imported into python programs with `import oqs`.

To run an example program:

	python3 examples/kem.py


2. #### Kademlia

Just run the following command:

```
pip install kademlia
```

3. #### QuanTurm itself

Download the project from the [Github repo](https://github.com/JosTorre/kademlia) or run:

```
git clone https://github.com/JosTorre/kademlia.git
```

### How to use it?

To run QuanTurm simply go to the project's main directory and run:

```
sudo python3 othermain.py
```

The program will ask for 3 inputs from your side:
- Post-quantum Signature Mechanism: you must type any of the names of the given list just as it is without the quotes.
- Number of Transactions per Block: 1 to N number of transactions needed per node to create a new block.
- Number of Blocks: 1 to N number of blocks wanted. As soon as the number of blocks is reached, the simulation will end.

At the end of the simulation, the total amount of running time as well as the storage used per node will be shown. The number of transactions and blocks created will also be shown at the end.

### Additional Notes

Not all of the signature schemes are supported yet. Because of either signature size or verification time, the simulation with some mechanisms might not be able to be run completely.
Using more than 30 nodes might cause problems at the moment.

I recommend running the program with *Falcon-512* as signature scheme.

### License 

The kind of license and distribution rights of this project are going to be defined later on.