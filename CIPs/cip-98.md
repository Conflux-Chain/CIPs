---
cip: 98
title: Fix bug of BLOCKHASH opcode in eSpace.
author: Chenxing Li(@ChenxingLi)
status: Final
type: Spec Breaking
created: 2022-06-14
---

## Simple Summary
Fix a bug in `BLOCKHASH` opcode in eSpace.

## Abstract
Fix a bug in `BLOCKHASH` opcode in eSpace. `BLOCKHASH` opcode in eSpace should regard the input as an epoch number, instead of the block number. 

## Motivation
Bug fix.

## Specification
`BLOCKHASH` opcode in eSpace regards the input as an epoch number, instead of the block number. The other behaviors are same as the core space. 

## Backwards Compatibility

This change is Spec breaking. 

## Test Cases
TBA.

## Implementation
TBA.

## Security Considerations
TBA.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
