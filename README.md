-----

# Voting System Smart Contract

This project contains a simple smart contract for a decentralized voting system, implemented in Solidity, and tested using Hardhat, Ethers.js, and Chai.

## Overview

The `VotingSystem` smart contract allows a set of predefined candidates to be voted upon by users. Each user can vote only once, and the contract can determine the winner based on the accumulated votes.

## Features

  * **Candidate Management**: Candidates are set during contract deployment.
  * **Single Vote Per User**: Ensures that each unique Ethereum address can cast only one vote.
  * **Vote Counting**: Tracks the number of votes for each candidate.
  * **Winner Determination**: Provides a function to retrieve the name of the candidate with the most votes.

## Technologies Used

  * **Solidity**: The smart contract programming language.
  * **Hardhat**: An Ethereum development environment for compiling, deploying, testing, and debugging your smart contracts.
  * **Ethers.js**: A library for interacting with the Ethereum blockchain, integrated with Hardhat.
  * **Chai**: An assertion library for Node.js and browsers, used with Hardhat for writing readable test assertions.

## Prerequisites

Before you can compile, deploy, or test this contract, you need to have Node.js and npm (or yarn) installed.

1.  **Node.js & npm**: Download and install from [nodejs.org](https://nodejs.org/).
2.  **Hardhat Project Setup**: If you don't have an existing Hardhat project, you can create one:
    ```bash
    mkdir voting-system
    cd voting-system
    npm init -y
    npm install --save-dev hardhat
    npx hardhat
    # Select "Create a JavaScript project" or "Create a TypeScript project"
    ```
3.  **Install Dependencies**: Ensure you have the necessary Hardhat, Ethers.js, and Chai packages installed in your project:
    ```bash
    npm install --save-dev @nomicfoundation/hardhat-toolbox @nomicfoundation/hardhat-chai-matchers @nomiclabs/hardhat-ethers ethers chai
    ```

## Contract (Conceptual)

*(**Note**: The actual Solidity contract code is not provided in your prompt, but this section describes what it likely contains based on the tests.)*

You would typically have a Solidity file named `VotingSystem.sol` (e.g., in the `contracts/` directory of your Hardhat project) that looks something like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract VotingSystem {
    string[] public candidates;
    mapping(uint256 => uint256) public votes;
    mapping(address => bool) public hasVoted;

    constructor(string[] memory _candidates) {
        require(_candidates.length > 0, "Must have at least one candidate");
        candidates = _candidates;
    }

    function vote(uint256 candidateIndex) public {
        require(candidateIndex < candidates.length, "Invalid candidate index");
        require(!hasVoted[msg.sender], "You have already voted!");

        votes[candidateIndex]++;
        hasVoted[msg.sender] = true;
    }

    function getWinner() public view returns (string memory) {
        require(candidates.length > 0, "No candidates available");
        uint256 winningVoteCount = 0;
        uint256 winningCandidateIndex = 0;

        for (uint256 i = 0; i < candidates.length; i++) {
            if (votes[i] > winningVoteCount) {
                winningVoteCount = votes[i];
                winningCandidateIndex = i;
            }
        }
        return candidates[winningCandidateIndex];
    }
}

```

## Testing

The provided JavaScript file contains the tests for the `VotingSystem` contract.

1.  **Save the Test File**: Save the provided JavaScript test code into a file named `votingSystem.test.js` (or similar) in the `test/` directory of your Hardhat project.

2.  **Run Tests**: From your project's root directory, execute the tests using Hardhat:

    ```bash
    npx hardhat test
    ```

### Test Cases

The tests cover the following scenarios:

  * **Successful Vote**: Verifies that a user can successfully cast a vote and their `hasVoted` status is updated.
  * **Double Voting Prevention**: Ensures that a user cannot vote more than once, and the transaction reverts with the expected error message.
  * **Winner Determination**: Checks that the `getWinner()` function correctly identifies the candidate with the highest number of votes.

## Deployment (Conceptual)

To deploy the contract to a local Hardhat network or a public testnet/mainnet, you would typically write a deployment script (e.g., `deploy.js` in the `scripts/` directory).

Example `deploy.js`:

```javascript
// scripts/deploy.js
const { ethers } = require("hardhat");

async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account:", deployer.address);

  const VotingSystem = await ethers.getContractFactory("VotingSystem");
  const votingSystem = await VotingSystem.deploy(["Alice", "Bob", "Charlie"]);

  console.log("VotingSystem deployed to:", votingSystem.target); // Use .target for newer ethers.js versions
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

To run the deployment script:

```bash
npx hardhat run scripts/deploy.js --network localhost
```

(Replace `localhost` with your desired network if deploying to a testnet like Sepolia or Amoy, after configuring it in `hardhat.config.js`).

-----
