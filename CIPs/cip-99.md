---
cip: 99
title: Make PoS Force Retire More Tolerant
author: Peilun Li (@peilun-conflux)
status: Final
type: Spec Breaking
created: 2022-06-27
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Allow more not-voting terms before we force-retire a node, and make the unlock period of a retiring node shorter to allow the node to rejoin the PoS voting faster.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
The current parameters make the node operation prone to causing force-retire and thus interest loss. This CIP proposes to make the parameters more tolerant, i.e., increase the number of not-voting terms needed and decrease the unlock period.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
The current PoS force-retires a node if a node in the committee does not cast any vote in a term, which is roughly an hour. However, the normal restart process of a node has been about 30-50 minutes, so if anything goes wrong when the node operator restarts/upgrades a node in the committee, this node is likely to be force-retired. And if the host machine encounters some random failure (like power loss), this 1-hour windows makes it almost impossible for the operator to respond in time.

Now since the force-retire is inevitable to happen sometimes, 7-day unlock period also looks too long to penalize an host mistake since there is no interest gain during this period. And this makes debugging the issue that causes force-retire more tiresome since we need to wait for 7 days before we can try again.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
The number of not-voting terms needed to force-retire a node becomes 3 continuous serving terms after the hardfork round. Note that if a node does not vote in the last 2 terms after it has been elected and is not elected into the committee in the next election, the node should not be force-retired.

The unlocking period becomes 1 day (both for normal retirement and force retirement) and the locking period becomes 13 days after the hardfork round.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The unlocking period of the normal retirement should not be longer than force retirement so no nodes will actively trigger force retirement.

Since every node serves the committee for 6 terms, checking 3 continuous terms helps regardless of the voting power of a node.

The total time for a node to lock and unlock is still at least 14 days, so this proposal does not introduce more attack chances.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This is spec-breaking.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
N/A.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
N/A.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
Allowing more not-voting terms make crash failure to affect the system longer. But if the voting power of this not-voting node does not prevent the system from making progress, having it in the committee for two more terms still leaves enough honest voting power.

Another issue is that the account that stakes before the hardfork height and withdraws after the height will be able to withdraw after less than 14 days. Since this only happens during the hardfork and not after, it should be okay.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
