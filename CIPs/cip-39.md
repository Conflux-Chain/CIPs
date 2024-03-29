---
CIP No.: 39
Title: Use Custom Field for Incompatible Upgrade
Author: Peilun (@peilun-conflux)
Discussions: https://github.com/Conflux-Chain/conflux-rust/pull/1978
Status: Final
Type: Spec Breaking
Created: 2020-12-08
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->


## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
The CIP proposes to use the first element of the `custom` field in block headers as the protocol version, and increase the version each time we need an imcompatible upgrade. Marking the blocks with different versions as invalid will get us a clean upgrade.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
The incompatible upgrades in Conflux do not necessarily invalidate blocks generated by "old-version" nodes. To keep the new-version chain "clean", this CIP proposes to use an increasing version number to force these blocks to be invalid in the new-version chain. The version number is stored in the first element of the `custom` field and is checked based on the block height. If users want to utilize the `custom` field, they can still append their customized data to this field.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Unlike Ethereum, Conflux does not invalidate blocks with a wrong state root or receipt root directly. These blocks are still included in the consensus graph, but if they become pivot blocks, they will be blamed by later blocks according to the blaming mechanism. If we want to make an incompatible change that results in different global states, the nodes that do not implement this change will generate blocks with wrong state roots and be blamed.

Although blamed blocks will not affect the system security, they do introduce extra overhead when we process blocks in the consensus graph or when we want to prove the value of a key for light clients. Thus, we want a mechanism that can invalidate these blocks before they are processed in the consensus graph, so we can avoid having blamed blocks when there are no active attackers.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
From the block height 3,615,000, blocks are required to set the first element of the `custom` field in the header to `[1]`. Blocks failing to satisfy this requirement will be regarded as invalid in the syncronization graph. 

Note: The validation rule will also mark all blocks referring to invalid blocks as invalid. Invalid blocks will not be considered in the consensus algorithm and will not be executed.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
This change does not require modification to the header struct. And we can directly check the validity from the header without knowing the graph structure.

Another choice is to only check the `custom` field for a period to invalidate "old-version" blocks. After the period, we can rely on the validation rule that all blocks referring to invalid blocks are invalid to keep invalidating "old-version" blocks. The benefit is that we do not need to reserve the first element of `custom` after this period. However, if the mining power stuck on the old version is too small so they cannot generate "invalid" blocks during the period, or they do not generate for over a checkpoint, their "old-version" blocks after the period will not be invalidated because they will only refer "new-version" blocks. This means we may still have blamed blocks from old miners. Since miners can still utilize the rest of the `custom` field, compared with possible issues, the benefit from this alternative design does not look worthwhile. 

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This CIP is incompatible with the current public Conflux chain and would cause an incompatible upgrade when enacted.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
A simple testcase is included in https://github.com/Conflux-Chain/conflux-rust-test-toolkits/blob/master/hard_fork_test.py. 

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
This has already been implemented in https://github.com/Conflux-Chain/conflux-rust/pull/1978.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
The blocks from "old-version" miners will be invalidated directly without affecting the consensus graph and will not affect the security of the "new-version" chain.

The blocks from "new-version" miners are still valid for "old-version" miners, just with different state root. The behavior of "old-version" miners is not within the consideration of this CIP.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

