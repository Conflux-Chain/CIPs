---
cip: 21
title: Minimum Storage Commitment per Epoch
author: Zhe Yang <yangzhe1990@gmail.com>
discussions-to: https://github.com/Conflux-Chain/conflux-rust/issues/1406
status: Draft
type: Spec Breaking
created: 2020-08-15
requires (*optional): <CIP number(s)>
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

In this CIP we propose to commit only the storage entries which become different at the end of the epoch execution from their value at the beginning of the epoch, existent or not. In other words, we commit only the absolute necessary changes to the storage, at the granular of an epoch level.

## Abstract

 A storage entry can be changed multiple times within a transaction, or within an epoch. We need a clear rule for those entries which change back-and-forth, whether or not these are considered "changed". It wasn't a matter for blockchains using a single Merkle Patrica Trie to keep the world state, where rewritting a same key-value had no change to the storage-root. However, because of the unique design of Conflux's storage structure, a rewrite of the same key-value to a storage entry changes the final storage-root value for the epoch. Thus the CIP.

## Motivation

The existing specification didn't define whether a storage entry is considered "changed" for the epoch commitment. Lots of redundant commitments has been made in the previous official rust implementation. For the reason explained aboved, Conflux must set the rule in its specification because iof its different storage structure.

## Specification

During the execution of the transactions, the world state of the ledger changes, and the changes are represented by storage entries.

In Conflux, the storage-root is computed at the level of an epoch. Transactions in an epoch are executed in order, and their changes are accumulated until the epoch execution is finished. A storage-entry is the serialized format of an account, a contract storage, contract code, or a vote list, etc. At the end of the epoch execution, we compare each updated storage-entry with its status at the beginning of the epoch. When a storage-entry remains the same value at the end of the execution, it will not be committed.

Example 1. Suppose the value of contract storage with storage key "k" is "v", during the epoch execution, it's first removed, then set back to "v", then this value is "unchanged" according to this CIP.

Example 2. Suppose an account has empty deposit list at the beginning of the epoch execution. It first deposit some balance then withdraw them. According to this CIP the storage-entry will not be updated for the deposit list.

Example 3. Suppose a contract has storage sponsorship activated. In the first transaction some storage is allocated, the storage collateral is substituted; in another transaction these storage are released, the storage collateral is added back. Besides, there are no transactions in the epoch changing any part of the contract. According to this CIP the storage-entry for the contract itself is "unchanged" because the serialized data remains the same.

## Rationale

The goal of the CIP is to clarify the set of storage entries to commit and its decision is made to bring up strong consistency over whether an update to a storage entry can be committed, to greatly reduce the complexity of the implementation, and to leave potential room for optimization at transaction execution level.

The existing implementation was made to commit each update of a storage-entries and its related account storage-entry. If we were to keep the existing implementation, the redundant commitment must be part of the specification to make sure that all implementations compute the same state root, and any inconsistency/update/error over which redundant storage-entry to commit would lead to a hardfork.

Omitting all possible (redundant) storage-entry commitment seems the best choice.

## Backwards Compatibility

Although not intended, this CIP is not backwards compatible with the early implementation. However this is a result of lacking clarification in the existing specification.

## Test Cases

See the provided examples.

## Implementation

https://github.com/Conflux-Chain/conflux-rust/pull/1721

## Security Considerations
There is no security impact of this CIP.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
