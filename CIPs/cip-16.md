---
CIP No.: 16
Title: Consolidate Contract Destruction Logic
Author: Chenxing Li (@ChenxingLi)
Discussions: https://github.com/Conflux-Chain/conflux-rust/issues/1747
Status: Final
Type: Spec Breaking
Created: 2020-08-05
Required CIPs: 12
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Put all the logic related to contract destruction to the post-processing of transaction execution. This includes recycling storage entries, refunding sponsor balance, etc.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Conflux introduces a sponsorship mechanism and a collateral mechanism to the Ethereum Virtual Machine. However, during contract destruction, the information for the new mechanisms is maintained at several different places. This brings a lot of issues to be solved. This proposal plans to move all the destruction-related logic to the post-processing of transaction execution. This includes removing storage entries, refunding collateral, refunding sponsor balance, etc.



## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Conflux introduces a sponsorship mechanism and a collateral mechanism to the Ethereum Virtual Machine. However, these changes bring some issues in the current implementation.

- First, the collateral for code may be refunded multiple times because a contract can be destructed multiple times in the same contract. An attacker can steal collateral using this bug.

- Second, the recycling of storage entries (removing storage entries from state and refunding collateral) is executed per epoch. If a contract is destructed and another contract is created at the same address within the same epoch, it may falsely read the obsolete storage entries, the staking vote list and the deposit list.

- Third, we require that the contract to be destructed has no storage collateral when it is killed by the `SUICIDE (0xff)` operation or by the `destroy()` internal contract function. (We call `SUICIDE (0xff)` and `destroy()` *killing commands* in the rest of this document.) But in case the killed contract acts as the storage owner via the sponsorship mechanism, it may become the storage owner of newly occupied storage after destruction.

In Ethereum, the killing command only transfers the remaining balance to a specified address and marks such contracts in the substate as *"to be killed"*. Before the end of transaction execution, the contract to be killed act as a normal contract. But when Conflux implements the sponsor mechanism, we falsely think the contract is dead after killing operations and refund the sponsor balance and storage collateral when executing the killing command. This implementation brings a lot of corner cases to be handled and we must continue to propose CIPs (2, 8, 12) to fix them.

Since Ethereum kills all the contracts (removing their code from the word-state) at the end of transaction execution, it is a good idea to move all the logic related to killing contracts to this place. This includes moving storage recycling and collateral and sponsor balance refunds to the same place where we remove code. And thus, the collateral mechanism doesn't need to deal with the killing command as a special case.

Since Conflux does not trigger an exception in the `SUICIDE (0xff)` operation due to collateral, it will also not trigger an exception in the `SUICIDE (0xff)` operation due to invalid refund address. So the `SUICIDE (0xff)` operation will not generate any exceptions for now. Instead, in case a contract's balance is refunded to an invalid address, the balance of the killed contract will be burnt.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Post-processing of transaction execution

Currently, after virtual machine execution, the transaction is post-processed as follows:

1. If the virtual machine execution succeeds, settle the storage collateral for all the storage changes during the contract execution. If the storage owner can not afford the collateral or the storage limit is not satisfied, an exception will be triggered and the state will be reverted to the version before the virtual machine execution.
2. Refund the unused gas fee. (At most a quarter of gas limit.)
3. Obtain the killed addresses during transaction execution from substate and remove their basic components (defined in the Conflux protocol specification) and code from the world state. Notice that in case the world state is reverted due to some exceptions, the killed addresses set in the substate must be empty.

This CIP will replace the third step with following steps,

1. Delete all the storage entries and code of killed contracts, and refund all the related collateral to their owner. Even if a contract is killed during the execution of this transaction, it can still receive collateral refunding.
2. Send the remaining sponsor balance of killed contracts to the corresponding sponsors. Even if a contract is killed during the execution of this transaction, it can still receive refunded sponsor balance.
3. Send the remaining balance of killed contracts to the refunding address in their last killing command. In case the refunding address is also in killed in the same transaction, the balance to be refunded will be burnt.

### Other changes
1. For a killing command, Conflux does exactly the same thing as Ethereum.
2. Conflux no longer recycles storage at the end of epochs since they are recycled during transaction execution.
3. In case a contract's balance is refunded to an invalid address, `SUICIDE (0xff)` will not trigger an exception. The balance of such contracts will be burnt.


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

The motivation part introduces the rationale for high level idea. Here we introduce the rationale for some details.

### No exception in storage recycling

Storage recycling is a costly step. If an exception happens (like a `NotEnoughBalanceForCollateral` exception) during or after recycling, the world-state will be reverted and the storage recycling becomes useless. This may open an attack vector. So we do not put collateral settlement together with contract destruction.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This CIP is not backwards compatible. It will be activated at the third phase of mainnet.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
This CIP resolves the bug that the code collateral can be refunded multiple times. It also considers a security issue when an exception happens during or after storage recycling.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
