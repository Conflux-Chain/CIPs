---
cip: <to be assigned>
title: Accelerate PoS Finalization
author: Peilun Li (@peilun-conflux)
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2023-05-29
requires (*optional): <CIP number(s)>
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Make the PoS finalization faster by shortening the PoS consensus round time and the pivot decision signing wait time.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Currently the PoS finalization time of Conflux is close to 10 minutes, significantly longer than the confirmation time of PoW and other blockchains. We can make the PoS finalization faster by shortening the PoS consensus round time and the pivot decision signing wait time.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Although the PoS finalization is designed as a fail-safe guarantee with a longer confirmation time, it's still worthwhile to make the extra PoS wait time comparable with the original PoW confirmation time. And the products that utilizes the PoS confirmation (like a high-value transaction or an on-chain light-weight client) will benefit from this optimization. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
The PoS round time is reduced from 60 seconds to 30 seconds. The defer time for validators to wait to sign a PoS-confirmed pivot block is reduced from 50 epochs to 20 epochs.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
Almost all PoS proposals and votes can be delivered by all validators with in 10 seconds, so in considering the latency, it's safe to reduce the round time to 30 seconds (leaving 15s for both proposals and votes). About the computing overhead, since most CPU/memory resources are used for PoW, doubling the PoS overhead is still acceptable.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This is Spec Breaking.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
N/A

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
N/A

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
N/A

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
