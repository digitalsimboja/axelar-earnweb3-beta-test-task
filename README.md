# Axelar cross-chain dApp examples

## Introduction

This repo provides the code for adding payment information to both the `call-contract` and `call-contract-with-token` contracts example dApps in the [Axelar Local Development Environment](https://github.com/axelarnetwork/axelar-local-dev). Examples contain both JavaScript and Solidity smart contract code.

## One-time setup

The complete steps to setup the repo is available at [axelar](https://github.com/axelarnetwork/axelar-local-gmp-examples#one-time-setup).

Clone this repo:

```bash
git clone https://github.com/digitalsimboja/axelar-earnweb3-beta-test-task.git
```

Build contracts and tests:

```bash
npm ci
npm run build
```

### How it works

Run the deploy and test code specific to each example described below.

To use defaults, substitute `${}` for any or all of the variables.

### Call contract

Relay a message from source-chain to destination-chain describing the reason for the message.

Deploy:

```bash
node scripts/deploy examples/call-contract [local|testnet]
```

Run the test:

```bash
node scripts/test examples/call-contract [local|testnet] ${"source-chain"} ${"destination-chain"} ${'message'} ${'description'}
```

#### Example

```bash
node scripts/deploy examples/call-contract testnet
node scripts/test examples/call-contract testnet "Fantom" "Polygon" 'Hello World' "Sending you greetings ahead of Christmas"
```

Output:

```
--- Initially ---
value at Polygon is
--- After ---
value at Polygon is Hello World
Payment information at Avalanche is Sending you greetings ahead of Christmas
```

### Call contract with token

Send aUSDC from source-chain to destination-chain and distribute it equally among all accounts specified adding reason under description for the payments.

Deploy:

```bash
node scripts/deploy examples/call-contract-with-token [local|testnet]
```

Run the test:

```bash
node scripts/test examples/call-contract-with-token [local|testnet] ${"source-chain"} ${"destination-chain"} ${amount} ${"description"} ${account} ${account2} ...
```

#### Example

```bash
node scripts/deploy examples/call-contract-with-token testnet
node scripts/test examples/call-contract-with-token testnet "Fantom" "Polygon" 10 "Here is payment for upkeep" 0xBa86A5719722B02a5D5e388999C25f3333c7A9fb
```

Output:

```
--- Initially ---
0xBa86A5719722B02a5D5e388999C25f3333c7A9fb has 100 aUSDC
Payment information at Polygon ""

--- After ---
0xBa86A5719722B02a5D5e388999C25f3333c7A9fb has 199 aUSDC
Payment information at Polygon is:  Here is payment for upkeep
```

### How I did it

Here are the modifications I added:

-   Added `description` storage variable to hold the payment information/reason for each transaction
-   Encoded the `description` received from the contract caller alongside the message value for the `call-contract` contract and alongside `destinationAddresses` for the `call-contract-with-token` which form the payload
-   Decoded the encoded `description` and `recipient(s)` values on the `_execute` function call body which is then stored within the contract alongside the message recipent(s)
