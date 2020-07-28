---
cip: 12
title: Tolerate non-existent sponsor for collateral
author: Chenxing Li(@Chenxing Li)
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2020-07-27
replaces: 2
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
We allow a contract with non-zero storage collateral to be destructed. In case refunding a storage collateral to a dead contract, the refunded token will be burnt. 

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Currently, we forbid the contracts with non-zero storage collateral to be destructed. This proposal plans to stop checking storage collateral during contract destruction. After the contract destruction, all the contract state will be removed from the world-state except its storage collateral if the collateral is non-zero. When some portion of the storage collateral is refunded to a dead contract, the refunded token will be burnt. 

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Currently, we forbid a contracts with non-zero storage collateral to be destructed to guarantee the dead contract is not the owner of any storage entry. However, some corner cases break this guarantee. Suppose the sender calls contract A and contract A sponsors the collateral for this transaction. If contract A calls itself and self-destructs, the outside executive can still execute as usual and occupy additional storage entries.

In order to handle this problem, CIP-2 proposed to forbid storage owner to be destructed in a sub-call. This proposal provides a more straightforward solution. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

In the `SELFDESTRUCT(0xff)` operation or the internal contract function `destroy()`, we no longer check whether `contract.storage_collateral>0`. However, if the `contract.storage_collateral>0`, the account field `storage_collateral` will be retained. Formally, the account state of contract will be reset to a default initialization value while the `storage_collateral` is retained. 

Each time we're refunding storage collateral to a contract, if its `sponsor_for_collateral` is the zero address, the `sponsor_balance_for_collateral` will not be updated, the refunded token will be burnt and the statistic information *total issued tokens* will be updated accordingly. 

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

There is a special case in the specification. In the current logic, when a contract is destructed, it will be removed from the world-state. (Or equivalently, be reset to a default initialization value.) If the dead contract is created again, the account field `storage_collateral` may be inconsistent with the actual collateral paid by such contract. When refunding collateral to the contract, such inconsistency may cause problems. So we retain the `storage_collateral` field.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This CIP changes the behavior of contract execution and further influences the world-state maintenance. So it is not backwards compatible. It will be activated in the next phase of mainnet.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
The sponsor of a contract may fail to retrieve its collaterals. This proposal brings no other security issues.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
