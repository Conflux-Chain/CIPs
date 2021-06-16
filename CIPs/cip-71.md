---
cip: 71
title: Configurable anti-reentrancy
author: Chenxing Li <chenxing@confluxnetwork.org>
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2021-5-18
---

## Simple Summary
Allow the contract developer to disable the anti-reentrancy for their contract. 

## Abstract
Currently, Conflux VM has an anti-reentrancy mechanism. When a contract appears twice in the call stack, Conflux will forbid all the subsequent write operations such as `SSTORE`, `LOG0` to `LOG4` and `CALL` with a non-zero balance. This CIP aims to make this feature configurable. A new internal contract should be introduced and allow the contract itself or its admin to turn on or turn off this feature. 

## Motivation
Conflux introduces an anti-reentrancy mechanism to avoid some attacks for Defi applications. However, this will also forbid some valid operations. For example, if a borrower contract calls a flash loan contract and the flash loan contract calls the "callback" function of the borrower contract. Then the borrower contract will appear twice in the call stack and all the subsequent write operations will be forbidden. Although the developers can avoid this issue by changing the interaction logic among contracts, this introduces additional tasks for contract migration.

## Specification

### Internal contract

Conflux should introduce a new internal contract 

```
pragma solidity >=0.4.15;

contract AntiReentrancy {
    /**
     * @dev the contract turns on/off the anti-reentrancy. 
     * @param allow A boolean variable to indicate if the reentrancy is allowed in this contract.
     */
    function allowReentrancy(bool allow) external {}

    /**
     * @dev a contract admin turns on/off the anti-reentrancy for the contract. 
     * @param contractAddr The address of the contract. 
     * @param allow A boolean variable to indicate if the reentrancy is allowed in this contract.
     */
    function allowReentrancyByAdmin(address contractAddr, bool allow) external {}


    /**
     * @dev get whether the reentrancy is allowed for a contract. vote power staking balance at given blockNumber
     * @param contractAddr The address of the contract. 
     */
    function isReentrancyAllowed(address contractAddr) external returns (bool) {}
}
```

### Change for the anti-reentrancy mechanism

Conflux will forbid the write operations (trigger the anti-reentrancy mechanism) only if a contract address that does not allow reentrancy appears twice in the call stack.

## Rationale

Now the contract developer can declare that "my contract will not trigger anti-reentrancy mechanism". But they can not declare that "my contract will ignore anti-reentrancy". These two declaration are different. For example, suppose the following call stack is the current call stack, contract A does not allow reentrancy but contract B allows reentrancy. When contract B is called for the second time, it cannot execute write operation, because contract A triggers anti-reentrancy mechanism. 

```
User -> Contract A -> Contract B -> Contract A -> Contract B
```

It is reasonable because it is equivalent to contract A uses `STATICCALL` to call contract B.


## Backwards Compatibility

This CIP is spec-breaking because it changes the execution process of some transactions. 

## Test Cases
TBA.

## Implementation
TBA.

## Security Considerations
Recently, some fully audited contract templates allow developers to detect the reentrancy and forbid malicious usage. So it is not necessary to retain the anti-reentrancy mechanism at the VM level. However, the deployed contract may rely on this mechanism. So instead of removing anti-reentrancy, we change it configurable. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
