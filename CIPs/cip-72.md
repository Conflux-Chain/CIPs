---
cip: 72
title: Accept Ethereum transaction signature
author: Chenxing Li <chenxing@confluxnetwork.org>
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2021-05-20
---

## Simple Summary

Make Conflux accepts the signatures signed by Ethereum's wallets, such as Metamask. 

## Abstract
Conflux has a different transaction format as Ethereum, so the Metamask cannot produce a transaction signature accepted by Conflux. This CIP plans to make Conflux treat the transactions with target epoch `u64::MAX` as Ethereum type transaction and uses a different way to verify transactions. 

## Motivation
This CIP will allow users to interact with smart contracts on the Conflux chain with Ethereum wallets such as Metamask. We believe this can attract more users to experience the Conflux chain. 

## Specification
If the target epoch of the transaction equals `u64::MAX`, Conflux treats this transaction as **Ethereum-like transaction**. The Ethereum-like transaction should use the following steps to verify. 

- The storage limit of this transaction is `u64::MAX`.
- The signature is signed for the hash value of `rlp(nonce, price, gas_limit, recipient, chain_id, ε, ε)`. Here, `ε` represents the "empty string" defined in the Conflux specification. 

Conflux does not require the sender's balance can afford the storage collateral for Ethereum-like transactions. 

When receiving an Ethereum-like transaction whose sender address does not start with `0x1`, the RPC should reject such a transaction.

## Rationale

We do not introduce an additional transaction format to avoid a massive effect on the network protocol, the database, and the block format. 

In this design, when a user signs a transaction using Metamask, any relayer can convert it into an Ethereum-like transaction on Conflux. 

The signatures signed for Ethereum transaction fields do not include the "storage limit". Therefore, any relayer can tamper with the "storage limit" in an Ethereum-like transaction. This CIP fixes the "storage limit" for all the Ethereum-like transactions. 

If an Ethereum address does not start with `0x1`, it will have a different address on Conflux. Thus, a user interacting with the Conflux chain may transfer its assets to an Ethereum address (a dead address). Conflux should help users avoid misbehavior, but such logic should not in the consensus part. In the consensus part, Conflux allows an Ethereum-like transaction to have the sender address without the prefix `0x1`. But Conflux RPC should reject the Ethereum-like transaction whose sender address does not start with `0x1`.


## Backwards Compatibility
This CIP is spec-breaking because it changes the signature verification for a particular type of transaction. 

## Test Cases
TBA.

## Implementation
TBA.

## Security Considerations

If a malicious relayer tries to tamper with the Ethereum-like transaction, it can not change it into a different transaction with can pass the signature verification. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
