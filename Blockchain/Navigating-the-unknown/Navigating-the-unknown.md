- [Navigating the Unknown](#navigating-the-unknown)
  - [Description](#description)
  - [Files](#files)
- [Instructions](#instructions)
  - [File examination](#file-examination)
    - [Two ports for every blockchain challenge](#two-ports-for-every-blockchain-challenge)
    - [Two smart contract source files for every blockchain challenge](#two-smart-contract-source-files-for-every-blockchain-challenge)
    - [Blockchain interaction requirements](#blockchain-interaction-requirements)
- [Step-by-step](#step-by-step)
  - [PLACEHOLDER TEXT](#placeholder-text)
    - [Method 1 - the simple way](#method-1---the-simple-way)
    - [Method 2 - blahblah](#method-2---blahblah)
    - [Method 3 - blahblahblah](#method-3---blahblahblah)
- [Flag](#flag)
  - [Issues encountered](#issues-encountered)
- [Lessons learned](#lessons-learned)
- [References](#references)
  - [Additional resources](#additional-resources)

# Navigating the Unknown

Navigating the Unknown is rated as Very Easy under the Blockchain section of Cyber Apocalypse 2023 (CA23).

## Description

> Your advanced sensory systems make it easy for you to navigate familiar environments, but you must rely on intuition to navigate in unknown territories. Through practice and training, you must learn to read subtle cues and become comfortable in unpredictable situations. Can you use your software to find your way through the blocks?

This challenge gives us a `.zip` file, as well as a docker container with 2 ports.

## Files

[Navigating-the-Unknown.zip](/Blockchain/Navigating-the-unknown/Navigating-the-Unknown.zip)

# Instructions

First, you will need to unzip the folder

```
$ unzip Navigating-the-Unknown.zip
```

This will extract the following files to your current directory:
* `blockchain_navigating_the_unknown` (Directory)
  * `Unknown.sol`
  * `Setup.sol`
  * `README.md`

## File examination

Running `file` with the wildcard `*` will show us file info for all of the files extracted

```
$ cd blockchain_navigating_the_unknown
$ file *
README.md:   ASCII text
Setup.sol:   Java source, ASCII text
Unknown.sol: ASCII text
```

Running `README.md` through a text editor will return some interesting info:
### Two ports for every blockchain challenge
*  `tcp` port
   *  The `tcp` port is used to retrieve info about connecting to the private chain (private key and targets contact address).**
*  `rpc` URL
   *  You will need this port in order to connect to the private chain
* You can determine which port is which by trying to connect to each with `netcat`; the one that works is the `tcp` port

### Two smart contract source files for every blockchain challenge
* `Setup.sol` - Contains a single contract, `Setup`
  * Initialization actions come from `Setup` from 3 functions
    * `constructor()` - Called once when contract is deployed
    * `TARGET()` - Returns the address of the challenge contract
    * `isSolved()` - Contains the final objective; returns `true` if solved
* The challenge file
  * Analyze their source carefully to figure out how to break them
    * The objective will be specified in `isSolved()`

### Blockchain interaction requirements

In order to work on blockchain challenges, you will need the following key requirements:

* Private key with ether
  * Ether is provided via `tcp` endpoint
* Target contract address
  * `Setup` and `Target` address both provide this
* `rcp` URL
  * Provided via a port in each blockchain challenge
* Use `web3py` or `web3js`
  * Use either web3 service in order to perform function calls in smart contract, or any other action needed

>Note: You could use `foundry-rs` instead of either `web3` choice. `foundry-rs` is an easy-to-use cli utility, but there are less examples online

# Step-by-step

Now that we know what the included files are, what they do, and some of the core requirements for blockchain challenges, let's look at some methods to solve this!

## PLACEHOLDER TEXT

PLACEHOLDER TEXT

### Method 1 - the simple way
kypanz claims this is the simple way. He's got the longest post, but looks to have a lot of screenshots. :shrug:

...

recap of what I covered in file exam

...

port 31092 doesn't work (it's rpc)
port 31700 works (it's tcp)

When we netcat to the `tcp` port, we get a prompt for various options

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

kypanz claims we will not need to use `web3js`, `web3py`, ether, etc. because "the code is not gonna be automated. So, instead we're going to use:
* Metamask (crypto wallet)
* Remix IDE (an IDE for solidity Smart contracts)
* Smart contracts (?)

(Paraphrasing) "We are going to interact with the smart contracts in a simpler way to understand what is happening on a general level."

Need to download Metamask

### Method 2 - blahblah

### Method 3 - blahblahblah

# Flag

HTB{}

## Issues encountered

* When I tried to do this, it blahed

# Lessons learned

1. I should learn about

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

* Link to something I found while writing this