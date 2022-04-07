---
cip: 94
title: On-chain DAO Vote for Chain Parameters
author: Peilun Li (@peilun-conflux)
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/95
status: Draft
type: <Spec Breaking>
created: <2022-03-31>
requires (*optional): <CIP number(s)>
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
This CIP proposes to use on-chain DAO voting to decide and update reward parameters without hardfork.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Currently, all reward parameters are hardcoded in the code. This CIP proposes to store these parameters as a part of the global state, so we can utilize the existing on-chain voting powers (by staking and locking CFXs through the internal `Staking` contract) to vote for parameter changes without a hardfork. The vote starts periodically (every few months), and only a fixed number of options (to increase the parameter, decrease the parameter, or keep it unchanged) are available. Accounts can cast their vote by sending transactions. The parameter state is updated when a vote period ends.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Traditionally, all chain parameter updates are incompatible changes and have to go through a hardfork. Since hardforks are expected to be infrequent and takes relatively a long time to prepare, we cannot update the parameters in time when the environment changes fast. 

Besides, whether to change a parameter and how to change it have often been decided by casting a governance DAO voting. However, it's still up to the developers to decide whether to follow these decisions and up to the miners to decide whether to apply this hardfork. This makes the voting less mandatory as we want it to be.

By integrating parameter update and the on-chain DAO voting, we can have a regular parameter update mechanism without hardfork.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

When the block number BN is executed, a new `ParameterControl` internal contract will be initialized the and the default parameters for PoW base reward and PoS reward interest will be stored as special storage entries KEY, NEXT_KEY. Besides the currently used parameter value, storage entries to store the summary of the on-going voting are also initialized for VOTING_KEY.

The list of parameters to vote are:

0. PoW base block reward.
1. PoS base reward interest rate.

And the options of each parameters are:

0. Remain unchanged.
1. Increase by 100%.
2. Decrease by 50%.

The internal contract `ParameterControl` has a struct `Vote` as 
```
struct Vote {
     uint8 index,
     uint8 opt_index,
     uint256 votes,
}
```

and two interfaces cast the vote and to read the vote. 

* `function castVote(uint64 version, Vote[] vote_data) external;`. The first parameter is a version number to indicate which vote period is the call for. The second parameter is an encoded list of `(uint8, uint8, uint256)` where the first element is the voted parameter index, the second element is the chosen option index, and the last element is the number of votes to cast on this option.
* `function readVote(bytes20 address) external view returns (Vote[])`. Read the votes data of an account.

An account can distribute his voting power to different options of the same parameter. For any parameter, the total votes for its options should not exceed the account's current total voting power. If the function is called for several times within a voting period, for each parameter, only the last successful call takes effect and overrides the previous votes. For example, if an account voted for both parameters in the first transactions TX1 and voted for parameter 2 in the second transaction TX2, its vote for parameter 1 remain the same as the one in TX1 and its vote for parameter 2 is changed to the one in TX2.

At the end of a voting period (counted as block numbers), NEXT_KEY is used to update KEY, and VOTING_KEY is read to compute and update NEXT_KEY.

An event is also emitted for each successful vote. The vote of each account will be stored in a list of entries following the key `prefix_and_hash(3, address)`. Specifically, for the parameter index `i` and the option index `j`, the number of votes for it is stored in `prefix_and_hash(3, address) + i * 3 + j`.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The rationale to store the voting results to NEXT_KEY instead of applying it immediately is that, if the result is manipulated unexpectedly, there is still a chance to revert it with a hardfork. And adding a delay allows more parameters (header-validity related, like difficulty) to be voted in the future, because the latest state for a new block may not be available.

We want an account to vote for multiple options for two reasons. One is that the PoS pool contract now can collect its users' choices and help to cast their votes. Another reason is that an account can combine these options to have a fine-grained option for a parameter. This allows us to make the options change range relatively large without a side effect.

We make later votes override early votes at the parameter level for efficiency reasons, because if an account only votes for one parameter, we only need to read the data for this parameter for validation. If we want every call to override all votes, we'll need to read all entries to see if they are voted before.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This is a breaking change because it adds a new internal contract and add new entries in the state. 

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
TBA

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
