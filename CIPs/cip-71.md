---
cip: 71
title: Disable anti-reentrancy
author: Chenxing Li <chenxing@confluxnetwork.org>
status: Final
type: Spec Breaking
created: 2021-5-18
---

## Simple Summary
Disable the anti-reentrancy for all the contracts. 

## Abstract
Currently, Conflux VM has an anti-reentrancy mechanism. When a contract appears twice in the call stack, Conflux will forbid all the subsequent write operations such as `SSTORE`, `LOG0` to `LOG4` and `CALL` with a non-zero balance. This CIP aims to disable this feature.

## Motivation
Conflux introduces an anti-reentrancy mechanism to avoid some attacks for Defi applications. However, this will also forbid some valid operations. For example, if a borrower contract calls a flash loan contract and the flash loan contract calls the "callback" function of the borrower contract. Then the borrower contract will appear twice in the call stack and all the subsequent write operations will be forbidden. Although the developers can avoid this issue by changing the interaction logic among contracts, this introduces additional tasks for contract migration.

## Specification

### Activation plan

Parameters: `BLOCK_NUMBER_CIP71`. 

Disable the anti-reentrancy for all the transactions when `block_number >= BLOCK_NUMBER_CIP71`. 

## Backwards Compatibility

This CIP is spec-breaking because it changes the execution process of some transactions. 

## Test Cases
TBA.

## Implementation
TBA.

## Security Considerations


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
