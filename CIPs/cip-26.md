---
cip: 26
title: Use timestamp from pivot block as TIMESTAMP (opcode 42)
author: Yanpei Liu(@resodo)
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2020-09-16
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Use timestamp from pivot block as TIMESTAMP (opcode 42), instead of the block under execution.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
During a transaction execution, the TIMESTAMP (opcode 42) is interpreted by timestamp from the block under execution. This will cause the timestamp of contract execution not to increase monotonically, and make it possible for some miners to do evil. The problem could be solved by using timestamps from the pivot block.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Currently, the TIMESTAMP (opcode 42) is interpreted by timestamp from the block under execution. This will cause the timestamp of contract execution not to increase monotonically, which is not the case from Ethereum contracts and could break the hidden assumption in some contracts from Ethereum. Besides that, a miner can do evil by mine a block with a manipulated timestamp.

One solution is to change the interpretation by the timestamp from the pivot block.

Thus, the timestamps of contract execution are monotonical again. And blocks with a manipulated timestamp are hard to be recognized as pivot block.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

When interpreting TIMESTAMP (opcode 42), use the timestamp from the pivot block.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

After this change, the timestamp of contract execution will be monotonical and make it harder for some miners to do evil.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This CIP is not backward compatible. 

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD


## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBD


## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
This change should enhance the security of the Conflux network.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
