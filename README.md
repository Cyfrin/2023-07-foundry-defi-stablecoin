# Foundry DeFi Stablecoin CodeHawks Audit Contest

<br/>
<p align="center">
<a href="https://codehawks.com" target="_blank">
<img src="https://res.cloudinary.com/droqoz7lg/image/upload/v1689007253/featured/zorxcgolkzoivtb5gubq.png" width="400" alt="Code Hawks first audit">
</a>
</p>
<br/>

## Contest Details 

- Total Prize Pool: $15,000
  - HM Awards: $14,000
  - LQAG Awards: $1,000
- Starts July 24, 2023
- Ends August 5th, 2023
- nSLOC: 236
- Complexity: 177

## Submissions 

- Submit to [CodeHawks](https://www.codehawks.com/contests/cljx3b9390009liqwuedkn0m0)

## In Scope

All contracts in `src` are in scope.

*Note on `script` folder*:
The contracts in `script` are the scripts you can assume are going to be used to deploy and interact with the contracts. If they have an issue that will affect the overall security of the system, they are in scope. However, if they have a security issue that only affects the script and not the overall deployment of the stablecoin protocol, it is out of scope.

## Scope
```bash
./src/
├── DSCEngine.sol
├── DecentralizedStableCoin.sol
└── libraries
    └── OracleLib.sol
```

Everything else is considered out of scope.

# Known Issues

The following issues can be ignored. 

- A known gas issue, is that we use storage variables instead of immutables for storing the addresses of the collateral. You can ignore this. 

- If the protocol ever becomes insolvent, there is _almost_ no way to recover. This is a known issue.

- EDIT: August 26th, for Judging
- We don't want the constructor marked as payable, as we like the extra protection it gives us from accidentally deploying a contract with ETH. 

# Differential Tests

You can find a nearly idential edition of this code in [Vyper here](https://github.com/Cyfrin/brownie-stablecoin-v23). For gas golfers, doing differential tests on these two contracts might be a great starting point. 

# About

This is Lesson 12 of the[Ultimate Foundry 27-hour Solidity Course](https://www.youtube.com/watch?v=umepbfKp5rI). 

This project is meant to be a stablecoin where users can deposit WETH and WBTC in exchange for a token that will be pegged to the USD. The system is meant to be such that someone could fork this codebase, swap out WETH & WBTC for any basket of assets they like, and the code would work the same.


- [Foundry DeFi Stablecoin CodeHawks Audit Contest](#foundry-defi-stablecoin-codehawks-audit-contest)
  - [Contest Details](#contest-details)
  - [Submissions](#submissions)
  - [In Scope](#in-scope)
  - [Scope](#scope)
- [Known Issues](#known-issues)
- [Differential Tests](#differential-tests)
- [About](#about)
- [Getting Started](#getting-started)
  - [Requirements](#requirements)
  - [Quickstart](#quickstart)
    - [Optional Gitpod](#optional-gitpod)
- [Usage](#usage)
  - [Start a local node](#start-a-local-node)
  - [Deploy](#deploy)
  - [Deploy - Other Network](#deploy---other-network)
  - [Testing](#testing)
    - [Test Coverage](#test-coverage)
- [Deployment to a testnet or mainnet](#deployment-to-a-testnet-or-mainnet)
  - [Scripts](#scripts)
  - [Estimate gas](#estimate-gas)
- [Formatting](#formatting)

# Getting Started

## Requirements

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  - You'll know you did it right if you can run `git --version` and you see a response like `git version x.x.x`
- [foundry](https://getfoundry.sh/)
  - You'll know you did it right if you can run `forge --version` and you see a response like `forge 0.2.0 (816e00b 2023-03-16T00:05:26.396218Z)`

## Quickstart

```
git clone https://github.com/Cyfrin/foundry-defi-stablecoin-codehawks
cd foundry-defi-stablecoin-codehawks
forge build
```

### Optional Gitpod

If you can't or don't want to run and install locally, you can work with this repo in Gitpod. If you do this, you can skip the `clone this repo` part.

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#github.com/PatrickAlphaC/foundry-smart-contract-lottery-f23)

# Usage

## Start a local node

```
make anvil
```

## Deploy

This will default to your local node. You need to have it running in another terminal in order for it to deploy.

```
make deploy
```

## Deploy - Other Network

[See below](#deployment-to-a-testnet-or-mainnet)

## Testing

We talk about 4 test tiers in the video. 

1. Unit
2. Integration
3. Forked
4. Staging

In this repo we cover #1 and Fuzzing. 

```
forge test
```

### Test Coverage

```
forge coverage
```

and for coverage based testing: 

```
forge coverage --report debug
```


# Deployment to a testnet or mainnet

1. Setup environment variables

You'll want to set your `SEPOLIA_RPC_URL` and `PRIVATE_KEY` as environment variables. You can add them to a `.env` file, similar to what you see in `.env.example`.

- `PRIVATE_KEY`: The private key of your account (like from [metamask](https://metamask.io/)). **NOTE:** FOR DEVELOPMENT, PLEASE USE A KEY THAT DOESN'T HAVE ANY REAL FUNDS ASSOCIATED WITH IT.
  - You can [learn how to export it here](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key).
- `SEPOLIA_RPC_URL`: This is url of the goerli testnet node you're working with. You can get setup with one for free from [Alchemy](https://alchemy.com/?a=673c802981)

Optionally, add your `ETHERSCAN_API_KEY` if you want to verify your contract on [Etherscan](https://etherscan.io/).

1. Get testnet ETH

Head over to [faucets.chain.link](https://faucets.chain.link/) and get some tesnet ETH. You should see the ETH show up in your metamask.

2. Deploy

```
make deploy ARGS="--network sepolia"
```

## Scripts

Instead of scripts, we can directly use the `cast` command to interact with the contract. 

For example, on Sepolia:

1. Get some WETH 

```
cast send 0xdd13E55209Fd76AfE204dBda4007C227904f0a81 "deposit()" --value 0.1ether --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY
```

2. Approve the WETH

```
cast send 0xdd13E55209Fd76AfE204dBda4007C227904f0a81 "approve(address,uint256)" 0x091EA0838eBD5b7ddA2F2A641B068d6D59639b98 1000000000000000000 --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY
```

3. Deposit and Mint DSC

```
cast send 0x091EA0838eBD5b7ddA2F2A641B068d6D59639b98 "depositCollateralAndMintDsc(address,uint256,uint256)" 0xdd13E55209Fd76AfE204dBda4007C227904f0a81 100000000000000000 10000000000000000 --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY
```


## Estimate gas

You can estimate how much gas things cost by running:

```
forge snapshot
```

And you'll see and output file called `.gas-snapshot`


# Formatting


To run code formatting:
```
forge fmt
```
