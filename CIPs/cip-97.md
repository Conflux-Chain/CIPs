---
CIP No.: 97
Title: Clear Staking Lists
Author: Chenxing Li(@ChenxingLi)
Status: Final
Type: Spec Breaking
Created: 2022-6-13
---


## Simple Summary
Remove account's staking lists. 

## Abstract
The staking lists of all accounts are removed in a lazy mode. After activating this CIP, when an account calls the `withdraw` interface of the staking internal contract with a non-zero `amount` for the first time, its staking list will be removed, and all the unsettled interest will be distributed to this account. 

## Motivation
Some PoS mining pools allow users to delegate tokens to their contracts and distribute rewards from participating PoS. However, the staking lists of PoS mining pool contracts keep growing. This makes the gas consumption of a single "deposit" or "withdraw" operation grow linearly and will exceed the maximum gas limit for a transaction. After Hydra hardfork, CIP-43 is activated, and no more interest will be generated from staking. It is meaningless to maintain such a staking list. So we should remove the staking list to resolve the gas consumption issue. 

## Specification
1. After this CIP is activated, for the first time an account calling the `withdraw` interface of the staking internal contract with non-zero `amount`, Conflux will distribute all the unsettled interest.
2. After the clearance of the staking list, the subsequent "deposit" and "withdraw" operations will not access the staking list. 
3. If an account makes a "deposit" operation before the clearance of its staking list, Conflux operations it as usual. 

### Gas Consumption
4. The gas cost for `withdraw` interface of staking internal contract changes to 400 (`SLOAD_GAS * 2`). 
5. After the clearance of staking list, the gas cost for `deposit` interface of staking internal contract changes to 10000 (`SSTORE_GAS * 2`). 

## Rationale
TBA.

## Backwards Compatibility
This change is spec breaking. 

## Test Cases
TBA.

## Implementation
TBA.

## Security Considerations
TBA.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
