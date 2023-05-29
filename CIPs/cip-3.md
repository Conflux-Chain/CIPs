---
CIP No.: 3
Title: Octopus Proof-of-Work Mining Algorithm
Author: Fan Long <longfan@confluxnetwork.org> and Dong Zhou <dong.zhou@confluxnetwork.org>
Status: Final
Type: Spec Breaking
Created: 2020-07-22
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
This is both a meta-CIP describing the design goals of Conflux Proof-of-Work algorithm and how we need to change it from the current PoW in the Oceanus network.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
The current Octopus mining algorithm is based on the memory chasing computation pattern adopted in ETHASH, a memory-hard pattern that was proven so far being reliable to defend against ASICs. However, large mining farms already hoards millions of GPUs dedicated for ETH mining. To encourage decentralization of the mining activities during the early phase of Conflux, we will introduce additional computation patterns that can favor high-end gaming GPU hards (NVIDIA Turing and AMD 5700s) against established low-end mining GPU cards such as (AMD 570s/580s).

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Mining decentralization is both economically desirable and security-wise
indispensable for a healthy blockchain system. Conflux will make mining GPU
friendly, which is the most practical and achievable path for decentralization.
One challenge Conflux faces is the established mining farms for ETH. It is
important to adjust Proof-of-Work algorithms in a way that can favor GPU models
in which individual community miners would like to own and discourage GPU
models that are already being extensively used by mining farms. Although as
Conflux grows the GPU mining landscape will evolve automatically (and
potentially will still favor large mining farms), such adjustment is still
critical for providing the security and the decentralization in the critical
launch phase of Conflux.

Specifically, after the change Octopus should accomplish the following design
goals:

1. The algorithm should remain as mostly memory-hard. It would be impossible to
solve the PoW one order magnitude faster without inventing faster/cheaper
memory.

2. Newer GPU cards (both AMD and NVIDIA) should have proper advantages over
older generations, ideally proportional to its cost difference.

3. We should strike a balance between having enough computation power to secure
the network after its launch and preventing mining farms (mostly right now for 
Ethereum mining) to gain too much advantages over community miners. 

4. To address the above trade-off, we will set the minimum memory requirement of 
participating mining to 6G at the begining of Conflux Tethys. In the future hard-fork
update, we will change the PoW once to raise the memory requirement bar to exclude 
6GB mining GPUs like P106.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
TBD with a PR to Conflux-Spec.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The rationales of the proposed Octopus algorihtm change are:

1. Introduce an appropriate amount multiplication and remainder computations that give new GPUs advantages over old GPUs.

2. The verification of the PoW is still as lightweight as verifying ETHASH.

3. Set the memory requirement for mining PoW at roughtly 4.5GB-5GB. 

4. The data connection between the computation and the memory-hard part is strong so that it is not plausible to speed up the computation with special hardware.

**Expected Speed:** The mining speed of NVIDIA GPUs on our reference implementation is as follows:

- 2080Ti: ~9.0M/s
- 2070: ~4.5M/s
- 2060: ~4.1M/s
- 1660s: ~2.8M/s
- 1080Ti: ~2.2M/s
- P104: ~1.3M/s
- P106: ~0.9M/s

Note that driven by profit, we expect that proprietary mining software developers will work on Conflux to optimize the miner implementation further. The mining speed therefore may improve in the future. 

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
The PoW change is not backward compatible. We will turn this on at epoch 0 of Tethys. 

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD with Miner implementation.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

Miner TBD.

The verification is fully implemented in Conflux-Rust once this [PR](https://github.com/Conflux-Chain/conflux-rust/pull/1878) is merged.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
PoW algorithm may cause significant security risk if there are holes in its design. To avoid this, we use ETHASH, an established memory-hard PoW, as the backbone of the algorithm.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
