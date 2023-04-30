- [Navigating the Unknown](#navigating-the-unknown)
  - [Description](#description)
  - [Files](#files)
- [Instructions](#instructions)
  - [File examination](#file-examination)
  - [Blockchain interaction requirements](#blockchain-interaction-requirements)
- [Step-by-step](#step-by-step)
  - [Method 1 - Metamask and Remix IDE](#method-1---metamask-and-remix-ide)
    - [Requirements](#requirements)
  - [Method 2 - Foundry-rs](#method-2---foundry-rs)
    - [Requirements](#requirements-1)
  - [Method 3 - web3py 1](#method-3---web3py-1)
  - [Method 4 - Using web3py 2 (ABI)](#method-4---using-web3py-2-abi)
  - [Method 5 - py-solc-x and web3py 3](#method-5---py-solc-x-and-web3py-3)
  - [Method 6 - ChatGPT (web3py 4)](#method-6---chatgpt-web3py-4)
- [Flag](#flag)
- [Lessons learned](#lessons-learned)
- [References](#references)
  - [Additional resources](#additional-resources)

# Navigating the Unknown

Navigating the Unknown is rated as Very Easy under the Blockchain section of Cyber Apocalypse 2023 (CA23).

## Description

> Your advanced sensory systems make it easy for you to navigate familiar environments, but you must rely on intuition to navigate in unknown territories. Through practice and training, you must learn to read subtle cues and become comfortable in unpredictable situations. Can you use your software to find your way through the blocks?

## Files

[Navigating the Unknown](/Blockchain/Navigating-the-unknown/Navigating-the-Unknown.zip)

> Note: You will also be given a docker container with two ports.

# Instructions

First, unzip the folder

```
$ unzip Navigating-the-Unknown.zip
```

This will extract a folder and several files into the current directory:
* `blockchain_navigating_the_unknown`
  * `Unknown.sol`
  * `Setup.sol`
  * `README.md`

## File examination

Run `file` with the wildcard `*` to see file info for the extracted files.

```
$ cd blockchain_navigating_the_unknown
$ file *
README.md:   ASCII text
Setup.sol:   Java source, ASCII text
Unknown.sol: ASCII text
```

Reading the contents of `README.md` will return some interesting info:

```
$ cat README.md
```

1. Two ports for every blockchain challenge
   1. `tcp` port
      1. The `tcp` port will return *info* about the private chain; it will show the private key and targets contact address.
   2. `rpc` URL
      1. Needed in order to *connect* to the private chain
      
    > Note: You can determine which port is which by trying to connect to each with `netcat` (`nc`) - the one that works is the `tcp` port

2. Two smart contract source files for every blockchain challenge
   1. `Setup.sol` contains a single contract: `Setup`
      1. Initialization functions come from `Setup`
         1. Function 1: `constructor()` - Called a single time, when contract is deployed
         2. Function 2: `TARGET()` - Returns the address of the challenge contract
         3. Function 3: `isSolved()` - Contains the final objective, returns `true` if solved
   2. The challenge file
      1. The way to break the challenge file will be specified in `isSolved()`

## Blockchain interaction requirements

To work on blockchain challenges, you need:

1. Private key with ether
   1. Ether is provided via `tcp` endpoint
2. Target contract address
   1. `Setup` and `Target` address both provide this
3. `rcp` URL
   1. Provided via a port in each blockchain challenge
4. Use `web3py` or `web3js`
   1. Either web3 service can perform function calls in smart contracts

> Note: You can use `foundry-rs` instead of `web3`. `foundry-rs` is an easy-to-use cli utility, but is poorly documented.

# Step-by-step

Now that we know what the included files *are*, what they *do*, and have the core requirements, let's start on the challnege and look at some methods to solve this!

* Port 31092 doesn't work (it's rpc)
* Port 31700 works (it's tcp)

Running `nc` against the `tcp` port prompts us with various options:

```
$ nc 165.22.1167 31700
1 - Connection Information
2 - Restart Instance
3 - Get flag
action? 
```
If we choose action 1

```
action? 1
Private key     : [snip]
Address         : [snip]
Target contract : [snip]
Setup contract  : [snip]
```

## Method 1 - Metamask and Remix IDE
[kypanz](https://kypanz.github.io/jekyll/update/2023/03/24/Navigating-the-unknown.html) and [0xJs](https://medium.com/@039305660xJs/htb-cyber-apocalypse-ctf-blockchain-8fa83e22f419) suggest using Metamask and Remix IDE.

### Requirements
* `Metamask` (A crypto wallet)
* `Remix IDE`

1. Open Metamask
2. The rpc URL is 165.22.116.7:31092, so the network we add in Metamask will read `http://165.22.116.7:31092`
3. In order to add the network, we need the `Chain ID`
   1. To get around this, enter dummy info into the field. The error message will show you the following:
   > The RPC URL you have entered returned a different Chain ID (**31337**). Please update the Chain ID to match the RPC URL of the network you are trying to add.
   2. Therefore, the Chain ID is `31337`.

> Note: After entering the correct Chain ID, you will see similar notification for Currency Symbol. You can change the Currency Symbol to GO, but it is not required.

You now have connected to the right *network*, but need to switch to the right *account*.

4. Profile -> Import Account
   1. Enter the `private key` gathered from the earlier connection:
   0xb331b8bcd8882a6d755ee6517d9124feba3563d7d8c0b969c1de10837a21e456
   2. You can confirm you have the correct account in `Metamask` by checking the `address` given in the `nc` connection.
5. After reviewing the contract code in Remix, compile it
6. Before deploying the contract, change the the `environment` to `Injected Provider - MetaMask`
   1. Changing the environmental variable here uses a web3 option for us!
7. Deploy `Setup.sol`
   1. After deploying in Remix, you will need to go back to Metamask and select Connect.
8. We will now instantiate a contract instead of deploying a new one.
   1. Instantiation means that we will use Remix to *create a copy* of the original contract, using the *same functions*. It will work the same because it has the same structure. We are not "recreating the wheel", we're copying what already exists.
9.  Grab the `Setup contract` address and put it into the `At Address` of the instantiated contract, in the `deployment` field in Remix.
10. Now instantiate the `Unknown.sol` contract using `Target contract` address. (It starts with "0x424..."). Again, put the address under the `deployment` field in Remix.
11. Expand the fields for `Setup` and `Unknown`. Input `10` into `Unknown` -> `updateSensor`. You will see a popup in Metamask asking you to confirm. Hit `confirm`.
12. Finally, reconnect to the server to retrieve the flag
```
$ nc 165.22.1167 31700
1 - Connection Information
2 - Restart Instance
3 - Get flag
action? 3
FLAG=HTB{9P5_50FtW4R3_UPd4t3D}
```

## Method 2 - Foundry-rs

[nikhilmemane09](https://medium.com/@nikhilmemane09/htb-cyber-apocalypse-2023-owning-smart-contracts-navigating-the-unknown-a5e821fd2bac), [SiriusA](https://sirius-a.github.io/ctf-writeups/writeups/2023/HTB-cyber-apocalypse/blockchain_navigating_the_unknown/), and [Cryptopone](https://gitlab.com/Cryptopone/cyberapocalypse2023-writeups/-/tree/main/Blockchain/Navigating%20the%20Unknown) advocate using foundryrs.

### Requirements

* [Foundry-rs](https://github.com/foundry-rs/foundry)

1. Examine both `Setup.sol` and `Unknown.sol`
   1. Make note of the function `updateSensors()` in `Unknown.sol`
2. `nc` the `tcp` port to gather the `private key` and `target address`
3. If you have not done so already, install `foundryrs` using `foundryup`
4. Run the foundryrs command `cast`
```
$ cast send --rpc-url=<rpc-url> --private-key=<your-private-key> <target-address> "<function-signature>" <argument>
```

> Note: The `function-signature` will be `updateSensors()`. The `argument` will be `10`.

5. To confirm whether the above cast was successful, check the function `isSolved()`
```
$ cast send --rpc-url=<rpc-url> --private-key=<your-private-key> <setup-address> "<function-signature>"
```

> Note that the `target-address` has been changed to `setup-address`. The `function-signature` will be `isSolved()`.

6. The response should look like:

> 0x000[snip]001

The `1` indicates that the challenge is solved.

7. `nc` into `tcp` to get the flag 

## Method 3 - web3py 1

[Hackbrain](https://hakbrain.notion.site/Navigating-The-Unknown-5d2fb22c652b4f1c91558269c0fdd81b) suggests using Python to solve the challenge. I don't understand the method well enough to speak to it. I simply encourage you to check out [his solution](https://hakbrain.notion.site/Navigating-The-Unknown-5d2fb22c652b4f1c91558269c0fdd81b), if it interests you.

## Method 4 - Using web3py 2 (ABI)

[thewhiteh4t](https://twc1rcle.com/ctf/team/ctf_writeups/cyber_apocalypse_2023/) offers a similar method using web3py to solve the challenge using `Application Binary Interface`

## Method 5 - py-solc-x and web3py 3

[ilter](https://ilter.tech/blog/htb-blockchain-navigating-the-unknown/) suggests using py-solc-x to compile the contracts, and then uses web3py.

## Method 6 - ChatGPT (web3py 4)

[LazyTitan33](https://github.com/LazyTitan33/CTF-Writeups/blob/main/HTB%20-%20CyberApocalypse_2023/Blockchain/Navigating_the_Unknown.md) mentioned a novel approach, using ChatGPT to get an answer with web3

# Flag

HTB{9P5_50FtW4R3_UPd4t3D}

# Lessons learned

1. Learn about `Metamask` and `Remix IDE`
2. Learn more about foundry-rs
3. Of the write ups I read, no one used web3js. Interesting!

# References

In no particular order

* https://hakbrain.notion.site/hakbrain/ccdf64ce51cd44b2ae1ee1f8200bfe37?v=95cb45fedd594c95babd434acf57b3f6&p=5d2fb22c652b4f1c91558269c0fdd81b&pm=s
* https://github.com/D13David/ctf-writeups/tree/main/cyber_apocalypse23/blockchain/navigation_the_unknown
* https://medium.com/@039305660xJs/htb-cyber-apocalypse-ctf-blockchain-8fa83e22f419
* https://github.com/SecYuri/HTB-Cyber-Apocalypse-2023-Writeups/tree/main/Blockchain_Navigating_the_unkown
* https://medium.com/@nikhilmemane09/htb-cyber-apocalypse-2023-owning-smart-contracts-navigating-the-unknown-a5e821fd2bac
* https://gitlab.com/Cryptopone/cyberapocalypse2023-writeups/-/tree/main/Blockchain/Navigating%20the%20Unknown
* https://github.com/LazyTitan33/CTF-Writeups/blob/main/HTB%20-%20CyberApocalypse_2023/Blockchain/Navigating_the_Unknown.md
* https://github.com/sbencoding/htb_ca2023_writeups/tree/master/blockchain/navigating_the_unknown
* https://kypanz.github.io/jekyll/update/2023/03/24/Navigating-the-unknown.html
* https://twc1rcle.com/ctf/team/ctf_writeups/cyber_apocalypse_2023/#navigating-the-unknown
  * This contains two different methods for Navigating the Unknown on one page
* https://sirius-a.github.io/ctf-writeups/writeups/2023/HTB-cyber-apocalypse/blockchain_navigating_the_unknown/
* https://ilter.tech/blog/htb-blockchain-navigating-the-unknown/

## Additional resources

* Metamask [download link](https://metamask.io/)
* Remix IDE [download link](https://remix.ethereum.org/)
* Foundry-rs [download link](https://github.com/foundry-rs/foundry)