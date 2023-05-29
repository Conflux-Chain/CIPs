---
CIP No.: 28
Title: Include Block Number in Contract Address Calculation
Author: Zhe Yang <yangzhe1990@gmail.com>
Status: Draft
Type: Spec Breaking
Created: 2020-09-24
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

In this CIP we propose to add block number in contract address calculation for normal contract creation. The contract creation corresponds to vm CREATE1 instruction.

## Abstract

Prior to this CIP normal contract creation follows the address scheme of FromSenderNonceAndCodeHash. However, the assumption that (sender, nonce, code_hash) triplet won't repeat is broken by dust_removal. We will create a new address scheme of FromBlockNumberSenderNonceAndCodeHash so that a contract address can not be reused manually except due to extremely unlikely hash collision.

## Motivation

Prior to this CIP, a sender `s` can create a seld-destructible contract `c` at nonce `n` with code `code`, then the sender clean up all its belongings so that in the next dust-removal the account is deleted from storage. As a result the nonce of `s` is reset to 0, which allows `s` to make transactions to bump nonce to `n - 1`. Then some transaction can trigger self-destruction for `c`, then sender `s` can deploy the contract again.

Please be noted that even though the `code` appears to be the same, the actually deployed contract can be different, because the contract can be initialized with a different contract state, and the contract behavior may change according to the contract state.

It's not a good idea to interact with a self-destructible contract, however it can happen in reality. Such contract can also be called by other contracts. If such contract can not be re-created at the same address, other users can notice and stop interacting with the deleted contract. But if such contract can be re-created, other users can not notice and can become victim if sender `s` had bad intention.

## Specification

Prior to this CIP, the contract address under address scheme FromSenderNonceAndCodeHash is calculated by `keccak(b"0x00" + sender_address(H160) + sender_nonce_little_endian(U256) + code_hash(H256))`.
In this CIP, we replace FromSenderNonceAndCodeHash with FromBlockNumberSenderNonceAndCodeHash, where contract address is calculated by `keccak(b"0x00" + block_number_little_endian(U64) + sender_address(H160) + sender_nonce_little_endian(U256) + code_hash(H256))`

## Rationale

The new address scheme prevent contract from being recreated at the same address because the block_number can't remain same after the dust_removal. The assumption on dust_removal is that an account to remove must be inactive for many epochs.

One may argue that the new address scheme makes the contract address unpredictable. Normal usage isn't affected because web3 interface retrieves the created contract address through transaction receipt. If a user wants predicatable contract address, the user should look for CREATE2 instruction.

## Backwards Compatibility

This CIP isn't backwards compatible. The new address scheme take place at epoch 0.

## Test Cases

No test case is provided.

## Implementation

https://github.com/Conflux-Chain/conflux-rust/pull/1853

## Security Considerations
This CIP addresses a potential security threat.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
