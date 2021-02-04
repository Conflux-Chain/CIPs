---
cip: 
title: Enable EC-related builtin contract
author: Chenxing Li (@ChenxingLi)
status: Draft
type: Spec Breaking
created: 2021-02-04
---

## Simple Summary
Enable big integer modular exponentiation and alt-bn128 related builtin contract. 

## Abstract
EVM has the following four builtin contract: `modexp` `alt_bn128_add`, `alt_bn128_mul` and `alt_bn128_pairing`, which have not been enabled in Conflux. It's the time to enable them!   

## Motivation
These builtin contract can support some crypto-related usage, e.g., verifying proof of zk-snark. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

We define the following parameters `cip-xx-activation-block`.

During executing transactions, if `current block number < cip-xx-activation-block`, the addresses `0x5`, `0x6`, `0x7` and `0x8` are regarded as normal contracts. If `current block number >= cip-xx-activation-block`, the addresses `0x5`, `0x6`, `0x7`, `0x8` implements builtin contracts `modexp` `alt_bn128_add`, `alt_bn128_mul` and `alt_bn128_pairing` respectively. The behavior and gas plan of these contracts inherits Ethereum's settings. ([EIP-196](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-196.md), [EIP-197](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-197.md), [EIP-198](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-198.md)). 

## Backwards Compatibility

This CIP is spec breaking. 

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations

No security considerations. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
