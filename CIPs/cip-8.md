---
cip: 8
title: Put off code collateral settlement. 
author: <Chenxing Li (@ChenxingLi)>
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2020-07-23
requires: 2
---

## Simple Summary

Let all the storage collateral be charged and refunded at the end of transaction execution. 

## Abstract

Currently, the storage collateral for the storage entries are charged or refunded at the end of transaction execution. But the collateral for contract code are charged or refunded at once. This CIP plans to make all the storage collateral be charged and refunded at the end of transaction execution. Meanwhile, in case of contract deconstruct, the refunding of sponsor balance for collateral will be put off to the end of transaction execution.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

EVM has an anti-intuition behavior. Suppose sender calls contract A, contract A calls contract B and contract B suicides, after that the contract A calls contract B again, then the contract B is executed as usual even if contract B has been deconstructed. 

This brings trouble for storage collateral. In the current specification, the code collateral is refunded in `SELFDESTRUCT (0xff)` operation. Since this operation can be executed in multiple times, the code collateral will be refunded multiple times. 

Moreover, each time the storage collateral is charged, a *Not enough balance for collateral* exception could be triggered. If this exception happens in a sub-message call, only the execution of callee will be reverted and the caller can still execute normally. Such corner cases bring unnecessary tasks in contract debugging. 

In the implementation view, the storage will not be write to disk or removed from disk until the transaction execution is done. So it is reasonable to move all the storage collateral charging and refunding to the end of execution. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Terms

When we refer *charge collateral* (or *refund collateral*), we does the following things:
- Update the balance of storage owner. 
- Update the field `collateral_for_storage` of storage owner in its basic account component. 
- Update the global parameter `collateral_for_storage`. 

The function `add_collateral_for_storage` and `sub_collateral_for_storage` in `core/src/state/mod.rs` of [conflux-rust (v0.6.0)](https://github.com/Conflux-Chain/conflux-rust/blob/v0.6.0/core/src/state/mod.rs) implements this logic. 

### Change the time points for charging and refunding collateral.

- Previous protocol 
    - **Post-processing of transaction execution.** Charge and refund all collaterals for the storage entries. (Except the entries whose owner is deconstructed during execution.)
    - **SELFDECONSTRUCT operation.** Charge and refund all collaterals for the storage entries whose owner is the deconstructed contract. Refund the collateral for code to *code owner*. 
    - **Destroy by internal contract.** Charge and refund all collaterals for the storage entries whose owner is the deconstructed contract. Refund the collateral for code to *code owner*.
    - **Contract creation.** Charge collaterals for contract code.
- Modified protocol
    - **Post-processing of transaction execution.** Charge and refund all collaterals for storage.

### Change the behavior of contract destruction

When a contract is deconstruct, the *previous* protocol updates the world state as the following steps at the time point of `SELFDESTRUCT (0xff)` operation or destroy function of the internal contract:

- Charge and refund all collaterals for the storage entries whose owner is the destructed contract.
- Check if the *storage collateral* of contract to be destructed is zero. Otherwise an exception will be triggered.
- Refund the collateral for code to code owner and update it *storage collateral* 
- Refund the sponsor balance.
- Refund the contract balance. 

The *modified* protocol updates the world state as follows. 
- Check if the *storage collateral* of contract to be destructed is zero. Otherwise an exception will be triggered.
- Deduct the collateral for code from *storage collateral* of code owner. (The *storage collateral* is updated immediately.)
- Refund the sponsor balance for gas only. 
- Refund the contract balance.

### Post-processing of transaction execution

In the modified protocol, the sponsor balance for collateral of all the destructed contract will be refunded after the settlement of all the collateral. 

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

TBA.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This proposal is not backwards compatible. 


## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
TBA.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
