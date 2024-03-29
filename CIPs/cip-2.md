---
CIP No.: 2
Title: Prohibit Storage Owner Destruction in Sub-Call
Author: Chenxing Li (@ChenxingLi)
Status: Superseded
Type: Spec Breaking
Created: 2020-07-22
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
This CIP is replaced by CIP-12.

During the execution of a transaction, if a contract acts as the storage owner via the sponsorship mechanism, then that contract should not be destructed in this execution unless such desctruction is called directly by the transaction sender, i.e. the call stack depth is zero. 

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
During the execution of a transaction, if a contract acts as the storage owner via the sponsorship mechanism, then that contract should not be destructed in this execution unless such destruction is called directly by the transaction sender, i.e. the call stack depth is zero. Otherwise, a VM exception will be triggered and the execution of sub-call fails. 

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Currently, we require that a contract can only be destructed when it occupies no storage. However if the contract is the storage owner of the execution causing its destruction, it may become the owner of newly occupied storage after its destruction in the same transaction execution. This breaks the assumption that no destructed contract occupies any storage. 

One solution is to move the check for storage collateral to the end of transaction execution. But the problem is that the contract can not determine whether the contract destruction succeeds during execution, while subsequent operations may depend on the return value of suicided contract. 

Thus, this proposal forbid the destruct storage owner. A exceptional case is, if the destruction is called directly by the transaction sender, the transaction execution will halt after destruction and we don't need to worry about this problem. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

When destructing a contract, before checking its collateral for storage, if the call depth is not zero and the contract to be destructed is the storage owner in this execution, a VM exception will be triggered. 

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
TBD


## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This CIP is not backwards compatible. But it is only different with the current specification in one corner case. If sender S calls contract A, contract A may call itself recursively and cause the destruction of contract A in a sub-call. If this transaction is sponsored (by contract A), the suicide will succeed in the old implementation and fail after this proposal is implemented. 

(Note, if contract A calls contract B and then contract B calls contract A, the reentrancy protection will be triggered.)

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD


## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBD


## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
TBD

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
