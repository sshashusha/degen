# WonderlaToken Smart Contract

## Overview

The `WonderlaToken` smart contract is an ERC-20 token with additional features for managing entry costs to a hypothetical amusement park called "Wonderla". The contract includes functionalities for minting, burning, transferring tokens, and redeeming entries based on token balance. It leverages OpenZeppelin libraries for secure and standardized implementations.

## Features

- **ERC-20 Compliance:** Implements standard ERC-20 functionality.
- **Owner-Controlled Minting:** Only the owner can mint new tokens.
- **Burnable Tokens:** Token holders can burn their tokens.
- **Transferable Tokens:** Enables token holders to transfer tokens.
- **Entry Cost Management:** Manages entry costs for different entry types.
- **Redeemable Entries:** Allows token holders to redeem entries by burning tokens.

## Prerequisites

- **Solidity:** ^0.8.0
- **OpenZeppelin Contracts:** ^4.0.0

## Installation

Install OpenZeppelin contracts:

```bash
npm install @openzeppelin/contracts
```

## Usage

### Deployment

Deploy the `WonderlaToken` contract by specifying the initial owner:

```solidity
constructor(address initialOwner) Ownable(initialOwner) ERC20("Wonderla Token", "WLT") {
    initializeEntryCosts();
}
```

### Minting Tokens

Only the owner can mint tokens:

```solidity
function minttokens(address recipient, uint256 amount) external onlyOwner {
    _mint(recipient, amount);
}
```

### Burning Tokens

Token holders can burn their tokens:

```solidity
function burntokens(uint256 amount) external {
    require(balanceOf(msg.sender) >= amount, "INSUFFICIENT TOKENS!");
    _burn(msg.sender, amount);
    emit Redeemed("Entries burned successfully");
}
```

### Transferring Tokens

Token holders can transfer tokens:

```solidity
function transfertokens(address recipient, uint256 amount) external {
    require(balanceOf(msg.sender) >= amount, "INSUFFICIENT TOKENS!");
    _transfer(msg.sender, recipient, amount);
}
```

### Redeeming Entries

Token holders can redeem entries by burning the required amount of tokens:

```solidity
function redeemEntryByName(string calldata entryName) external {
    EntryType entryType = getEntryTypeByName(entryName);
    uint256 cost = entryCosts[entryType];

    require(cost > 0, "Invalid entry type");
    require(balanceOf(msg.sender) >= cost, "INSUFFICIENT TOKENS!");

    lastRedeemedEntry = entryType;
    _burn(msg.sender, cost);
    emit Redeemed("Entry redeemed successfully");
}
```

### Updating Entry Costs

Only the owner can update entry costs:

```solidity
function updateEntryCost(EntryType entryType, uint256 cost) external onlyOwner {
    entryCosts[entryType] = cost;
}
```

### Viewing Token Balance

Users can view their token balance:

```solidity
function getMyTokenBalance() external view returns (uint256) {
    return balanceOf(msg.sender);
}
```

### Viewing Last Redeemed Entry

Users can view the last entry type they redeemed:

```solidity
function getLastRedeemedEntry() external view returns (EntryType) {
    return lastRedeemedEntry;
}
```

## Internal Functions

### Initialize Entry Costs

Initialize default entry costs:

```solidity
function initializeEntryCosts() internal {
    entryCosts[EntryType.Child] = 800;
    entryCosts[EntryType.Adult] = 1200;
    entryCosts[EntryType.Special] = 0; // Special entry is free
}
```

### Get Entry Type by Name

Convert entry name to `EntryType`:

```solidity
function getEntryTypeByName(string calldata entryName) internal pure returns (EntryType) {
    if (compareStrings(entryName, "Child")) {
        return EntryType.Child;
    } else if (compareStrings(entryName, "Adult")) {
        return EntryType.Adult;
    } else if (compareStrings(entryName, "Special")) {
        return EntryType.Special;
    } else {
        revert("Invalid entry name");
    }
}
```

### Compare Strings

Utility function to compare strings:

```solidity
function compareStrings(string memory a, string memory b) internal pure returns (bool) {
    return keccak256(abi.encodePacked(a)) == keccak256(abi.encodePacked(b));
}
```

## License

This project is licensed under the MIT License.
