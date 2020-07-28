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
Currently, we forbid the contracts with non-zero storage collateral to be destructed. This proposal plans to stop checking storage collateral during contract destruction. If a contract has the same address of a killed contract, it may receive refunding collateral for the killed contract. So each time a contract receives a collateral refunding, no matter who paid this collateral, the part that exceeds the current storage collateral will be refunded to *sponsor balance for collateral* and the rest part will be burnt. 

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Currently, we forbid a contracts with non-zero storage collateral to be destructed to guarantee the dead contract is not the owner of any storage entry. However, some corner cases break this guarantee. Suppose the sender calls contract A and contract A sponsors the collateral for this transaction. If contract A calls itself and self-destructs, the outside executive can still execute as usual and occupy additional storage entries.

In order to handle this problem, CIP-2 proposed to forbid storage owner to be destructed in a sub-call. This proposal provides a more straightforward solution. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

In the `SELFDESTRUCT(0xff)` operation or the internal contract function `destroy()`, we no longer check whether `contract.storage_collateral>0`. 

Each time we're refunding storage collateral to a contract, let `v = min(refunding_collateral, contract.storage_collateral)`. The contract can only receive `v` refunding collaterals and the reset part will be burnt. Formally 

```
contract.storage_collateral -= v
contract.sponsor_balance_for_collateral += v 
```

The global statistic values are update as follows
```
total_storage_tokens -= refunding_collateral
total_issued_tokens -= refunding_collateral - v
```

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

The `total_storage_tokens` and the `total_issued_tokens` are updated at the time point of refunding collateral other than killing contract. Because the storage entries owned by killed contract should continue to generate collateral interest until they are released. 

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
