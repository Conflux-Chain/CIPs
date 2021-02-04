---
cip: 27
title: Remove whitelist keys at contract removal.
author: Zhe Yang <yangzhe1990@gmail.com>
discussions-to: Internal discussion
status: Final
type: Spec Breaking
created: 2020-09-20
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

In this CIP we propose to remove whitelist keys for a contract at the time when the contract itself is removed.

## Abstract

A contract can be deleted by `selfdestruct` (corresponds to SUICIDE opcode), or by calling `destroy` of admin internal contract. However it was forgotten to remove whitelist keys for the contract.

## Motivation

Nothing more than the Abstract section.

## Specification

The whitelist keys of a contract will be deleted at the time when the contract itself is removed. The removal can happen at `selfdestruct`, or `destroy` of admin internal contract call. The removal may happen at dust-removal as well.

## Rationale

Nothing to see here.

## Backwards Compatibility

This CIP changes the world-state content, therefore the state-root of the blockchain.

## Test Cases

Test case 1.

Initialize contract account `C` with two whitelist keys `W1`. `W2`

In an epoch, transaction `t1` destroys `C`, remove the whitelist keys `W1`, `W2` in transaction `t2`, `t3`, remove a non-existent whitelist key `W3` in transaction `t4`. The execution of `t2`, `t3` and `t4` should be equivalent.

After the epoch transaction, assert that the whitelist keys `W1` and `W2` are moved from the storage.

## Implementation

https://github.com/Conflux-Chain/conflux-rust/pull/1843

## Security Considerations
There is no security impact of this CIP.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
