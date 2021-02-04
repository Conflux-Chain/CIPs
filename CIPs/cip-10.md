---
cip: 10
title: Mining Reward Finalization
author: Fan Long <longfan@conflux-chain.org>
discussions-to: <URL>
status: Final
type: Spec Breaking
created: 2020-07-24
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Change the initial mining reward to 7 CFX per block. The decay of the mining reward starts at the 48 months. It decays every three months in a speed that will cause halving every four years. It stops decaying once it reaches 1.75 CFX per block.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Adjust the block reward to its final number for the Phase 3 network launch.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
The amount of block reward is critical for the economical and technical security of Conflux. It can be viewed as a collective fee payed by the community to the miners for maintaining the security of the network. We want to avoid either overpaying or underpaying.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
When assigning block reward, for the first *252288000* blocks, the block reward will remain constant as 7 CFX. Afterward, for every *15768000* blocks, the block reward will decay with a factor of *0.958*. Until it reaches the reward amount of 1.75 CFX. Therefore, the first decay will happen at *252288000 + 15768000*. The last decay will happen at the block number *2270592000*.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
We believe 7 CFX is a good amount because it puts Conflux to generate block reward on par (and slightly higher) with other major GPU minable coins like RVN, Grin, etc.. It gives Conflux enough edge to attract miners from the start.

We expect block reward would be the main income of miners for at least 3-4 years. We want to maintain this constant so that we have a stable environment to grow the network. As the network grows, miner will have both transaction fees and stake interests as additional income sources.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This is a specification breaking change.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
It is tested in:

tests/secondary_reward_test.py
tests/rpc/test_get_block_reward_info.py

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
This PR in Conflux-Rust implements this change:

https://github.com/Conflux-Chain/conflux-rust/pull/1709

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
There is no security related consideration about this change.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
