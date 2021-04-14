---
description: This is the first tutorial of the HardHat series on Celo blockchain. In this series we will learn how we can use Hardhat which is an alternative to popular Truffle development environment on the Celo Blockchain
---

# How to use Hardhat on Celo blockchain

## Introduction
Hardhat is a development environment that facilitates building on EVM compatible blockchains.
It helps developers manage and automate the recurring tasks that are inherent to the process of building smart contracts and dApps, as well as easily introducing more functionality around this workflow. This means compiling and testing at the very core.

## Prerequisites
1. Prior knowledge of Solidity and smart contracts deployment
2. Node-JS and Yarn

## Setup
We'll install Hardhat using the npm CLI.
In the terminal run these commands

```
mkdir celo-hardhat-tutorial 
cd celo-hardhat-tutorial 
npm init --yes 
yarn add --dev hardhat 
```

It will install Hardhat and all it's dependencies in a bit. After the installation is complete, use the command

```
yarn hardhat
```

-photo

Select **Create an empty hardhat.config.js** from the options.

If everything went well, we should have the following files in our working directory.
```
hardhat.config.js  node_modules  package.json  package-lock.json
```

## Writing Smart contract
We're going to create a simple smart contract that implements a token that can be transferred. 
Token contracts are most frequently used to exchange or store value. We won't go in depth into the Solidity code of the contract on this tutorial.

Create a new directory `contracts` in the root directory and create a file inside the directory called `Token.sol`.

Paste the following code inside the **Token.sol**

```
// SPDX-License-Identifier: GPL-3.0
// Solidity files have to start with this pragma.
// It will be used by the Solidity compiler to validate its version.
pragma solidity ^0.7.0;


// This is the main building block for smart contracts.
contract Token {
    // Some string type variables to identify the token.
    // The `public` modifier makes a variable readable from outside the contract.
    string public name = "My Hardhat Token";
    string public symbol = "MBT";

    // The fixed amount of tokens stored in an unsigned integer type variable.
    uint256 public totalSupply = 1000000;

    // An address type variable is used to store ethereum accounts.
    address public owner;

    // A mapping is a key/value map. Here we store each account balance.
    mapping(address => uint256) balances;

    /**
     * Contract initialization.
     *
     * The `constructor` is executed only once when the contract is created.
     */
    constructor() {
        // The totalSupply is assigned to transaction sender, which is the account
        // that is deploying the contract.
        balances[msg.sender] = totalSupply;
        owner = msg.sender;
    }

    /**
     * A function to transfer tokens.
     *
     * The `external` modifier makes a function *only* callable from outside
     * the contract.
     */
    function transfer(address to, uint256 amount) external {
        // Check if the transaction sender has enough tokens.
        // If `require`'s first argument evaluates to `false` then the
        // transaction will revert.
        require(balances[msg.sender] >= amount, "Not enough tokens");

        // Transfer the amount.
        balances[msg.sender] -= amount;
        balances[to] += amount;
    }

    /**
     * Read only function to retrieve the token balance of a given account.
     *
     * The `view` modifier indicates that it doesn't modify the contract's
     * state, which allows us to call it without executing a transaction.
     */
    function balanceOf(address account) external view returns (uint256) {
        return balances[account];
    }
}
```

Let's compile our contract to see if our code is fine.
Run the command 
```
npx hardhat compile
```

-photo

Our smart contracts have been compiled successfully.

## Testing 
Writing tests when building smart contracts is of crucial importance. In our tests we're going to use ethers.js to interact with smart contracts.
Create a new directory called `test` inside our project root directory and create a new file called `Token.js`.


