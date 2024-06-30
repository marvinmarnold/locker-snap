# Locker HODL

Locker HODL is a fork of Coinbase Smart Wallet that automatically saves and locks-up a little for you every time you use it.

As an unofficial fork, this wallet cannot be natively imported into wallet.coinbase.com.
To get around this limitation, Locker HODL is packaged into a Metamask snap.
Now you can use your Coinbase Smart Wallet from Metamask too.

Getting native tokens onto new wallets can be a pain.
Locker HODL pays gas for you and bills your card via Stripe.

## Where to find it

- Video: https://www.loom.com/share/93ecca145ced48c2bccf0343d4f481c3?sid=c55a9571-f441-439a-8f81-aaee5c4b3c8a
- Base Sepolia
  - implementation: [0x3a7B8aC9d9565EB9D32De95356e113190F857aB4](https://basescan.org/address/0x3a7B8aC9d9565EB9D32De95356e113190F857aB4)
  - factory: [0x86FC046543717A67b03847BEDE9560FB4368d274](https://basescan.org/address/0x86FC046543717A67b03847BEDE9560FB4368d274)

Unable to deploy to production. See notes in `Challenges`.

## Why you should care

Not everyone flosses at night and puts 20% of their savings into a diversified saving strategy.
Locker HODL is for the everyday crypto user that wants basic savings on-chain.
Imagine your returns today if you just put 1% of your ETH volume into savings starting 5 years ago.

Locker HODL can also simplify your on-chain transactions.
Now you can pay for your gas with credit card via Stripe and we'll pay on-chain for you.

## Context

No code from Locker has been reused. This repo was started fresh just for the hackathon.

Locker HODL was first conceptualized at ETH Denver as [Auto HODL](https://devfolio.co/projects/auto-hodl-e5dd).
At that point, MetaMask Snap was not yet stable, so savings was implemented as a standalone proxy contract.

Since ETH Denver, Auto HODL was rebranded as [Locker](https://locker.money).
Locker automates inbound payroll deposit for web3 contractors.

## How it's made

- Metamask Snap account abstraction template
- Added foundry and [copied in](https://github.com/marvinmarnold/locker-snap/tree/cd7c14cd8adfd9a9c76494f17a1b04e0834aed37/packages/snap/contracts/coinbase) Coinbase Smart Wallet
- [Modify](https://github.com/marvinmarnold/locker-snap/blob/cd7c14cd8adfd9a9c76494f17a1b04e0834aed37/packages/snap/contracts/coinbase/CoinbaseSmartWallet.sol#L24) Coinbase Smart Wallet code for saving features
  - internal treasury that you can't spend beyond
  - time-lock

## Challenges

### Getting Coinbase Smart Wallet to play nice with Snaps

- Snaps uses hardhat, CSW uses foundry
- For some reason, unable to interact with smart contract from [some Snap code](https://github.com/marvinmarnold/locker-snap/blob/cd7c14cd8adfd9a9c76494f17a1b04e0834aed37/packages/snap/src/keyring.ts#L206). Had to hardcode values per wallet.
- Snaps is very sensitive to different bundler and RPC providers.
- Was not able to reliably execute transactions through Snap for the smart wallet. Still debugging root causes.

### Coinbase Smart Wallet module uncertainty

- Ideally would like to implement savings logic as a module so that it can be installed into existing CSW.
- CSW does not currently support ERC-7579 or ERC-6900. No clear message from team what direction it's headed.

### Gatsby site deployment

Something about the workspaces structure is forcing the foundry code to compile unnecessarily for the Gatsby site, leading to build errors.
Site also needs to communicate with snap server which is hard to coordinate in prod in time for submission deadline.

### Custody

To make the CSW non-custodial, a private key needs to be supplied that owns it.
This is currently pasted into the UI which is awkward and not secure.

## Technologies

Coinbase smart wallet, Stripe, Metamask Snaps, Gatsby, Foundry, Hardhat, Biconomy.

## Local development

### Deploy contracts

1. Go to contract code: `cd packages/snap`
1. Setup cast account: `cast wallet import hack2  --private-key PRIVATE_KEY_WITHOUT_0x`
1. Setup env vars: `cp .env.sample .env`
1. Deploy CoinbaseSmartWalletFactory: `make deploy`
1. Copy deployment addresses for later

### Setup snap

There is an issue querying the address for a new account from the factory.
You must hardcode the new account address in the snap code.
To do this, interact with the factory you deployed, to get an address

```
cast call FACTORY_ADDRESS --rpc-url RPC_URL  --account CAST_ACCOUNT_NAME "getAddress(bytes[],uint256)" "[0xABI_ENCODED_OWNER]" 0x73c31044ac380f9d678c3a66715e07128c84b728ad7ac39c7c176b80e5fabaf9
```

0x73.. is an arbitrary salt

## Credit

Designs made by [@iiankitadixit](https://x.com/iiankitadixit).
