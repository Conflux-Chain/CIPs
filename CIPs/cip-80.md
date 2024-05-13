---
CIP No.: 80
Title: Align Conflux and Ethereum Address Generation Rules
Author: Chenxing Li (lylcx2007@gmail.com)
Status: Superseded
Type: Spec Breaking
Created: 2021-8-31
Required CIPs: 72
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Currently, all the private-key-controlled addresses start with `0x1` in hex format. This CIP plans to remove this constraint. So Conflux can produce the same address as Ethereum with the same private key. 

## Abstract

CIP-72 introduces Ethereum-Like transactions, which make it possible to interact with Conflux Network by Ethereum wallet like metamask. However, Conflux has a different way to recover sender address from signature, and thus the same private key may produce different address. It significantly introduces the risk that the users transfer their assets to a dead address by mistake, and further impedes users experience Conflux by Ethereum wallet.

This CIP plans to make Conflux and Ethereum have the same address with the same private key, while the existing Conflux address will not be effected. Conflux has two types of transactions: normal transaction and Ethereum-like transaction. The normal transaction will retain the original signature recover process and the Ethereum like transaction will recover signature in the same rule as Ethereum. If a user has address `0x40a...` on Ethereum, it will control both address `0x10a...` (by sending normal transaction) and `0x40a...` (by sending Ethereum-like transaction) on Conflux. 


## Motivation

Explained in abstract.

## Specification

### Signature recover

### Internal contracts
- For the other internal contracts except `AdminControl`, each time we want to check if an address is a contract, we check its `code_hash` and whether the target address is in the call stack.
- For `AdminControl`, we accept any address as admin.

### State root
Currently, the world state stores the private-key-controlled account and contract account in different format. This format will influence the state root. Even if an address started with `0x8` has not been initiated, it will be stored in format of a contract account. After CIP-80, we apply the following rule:
- If an address starts with `0x8`, it will be stored in format of a contract account, even if it is a private-key-controlled address. 
- For the other address, it will be stored in format of a contract account only if it has code. 

### Address validity check in EVM
The following address validity check will be removed.
- The receiver address of a transaction or a message call. 
- The refund address for suicide. 
- The admin address. 

We never checked the following address before, because they are always valid. 
- The sender of a transaction.
- The sponsor of a contract.

### Block validity
If a transaction has invalid receiver address, a block packing such transaction will not be regarded as invalid. So this CIP does not have block validity issue.


## Rationale

TBA.

## Backwards Compatibility

TBA.

## Test Cases

TBA.

## Implementation

TBA. 

## Security Considerations

TBA.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
