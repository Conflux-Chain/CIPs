---
CIP No.: 8
Title: Delay Code Collateral Settlement
Author: <Chenxing Li (@ChenxingLi)>
Status: Final
Type: Spec Breaking
Created: 2020-07-23
Required CIPs: [12](cip-12.md)
---

## Simple Summary

Let all the storage collateral be charged and refunded at the end of transaction execution.

## Abstract

Currently, most storage collateral settlements are executed at the post-processing of transaction execution. But the collateral for contract code are charged or refunded at once. This CIP plans to make all the storage collateral be charged and refunded at the end of transaction execution. Meanwhile, in case of contract deconstruct, the refunding of sponsor balance for collateral will be put off to the end of transaction execution.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Each time the storage collateral is charged, a *Not enough balance for collateral* exception could be triggered. If this exception happens in a sub-message call, only the execution of callee will be reverted and the caller can still execute normally. Such corner cases bring unnecessary tasks in contract debugging and make the collateral mechanism hard to understand.

In the implementation view, the storage will not be written to disk or removed from disk until the transaction execution is done. So it is reasonable to move all the storage collateral charging and refunding to the end of execution.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

CIP-16 will handle all the logics about contract destruction. So we skim collateral information maintenance in contract destruction here.

Besides contract destruction, the collateral of contract code will be charged in transaction post-processing other than in contract creation.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

There is no more details to be explained.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This proposal is not backwards compatible. It will be activated in the third phase of mainnet.


## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
If the suicide operation is called multiple times in one transaction, the code collateral will be recorded multiple times. This is a known issue which is not brought from this CIP. It will be fixed in the CIP-16.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
