---
CIP No.: 34
Title: Contract-Friendly Verification Rules
Author: Guang Yang (@Regulusyg)
Status: Draft
Type: Spec Breaking
Created: 2020-11-01
---

## Simple Summary

Enable verification of Conflux status with simple rules inside a smart contract (e.g. an Ethereum contract).

## Abstract

The current light client confirmation rule in Conflux requires verifying tens to hundreds of block headers, which is acceptable for light client wallets but way too expensive for a smart contract. However, implementing contract-level light clients, i.e. similar to BTCRelay on Ethereum, on other blockchains is important for cross-chain interoperability. For these use cases, especially for blockchains like Ethereum where in-contract computation is costly and the confirmation latency is already dominated by latency of Ethereum, it turns out more imperative to have simple rules rather than fast confirmation.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

For cross-chain interoperability it is important to implement contract-level light clients so that the status and event on one chain can be automatically verified on the other chain. For example, an Ethereum smart contract is able to verify whether a Bitcoin transaction is confirmed with high confidence, by verifying an SPV proof consisting of a Merkle proof  (showing the target transaction is included in a Bitcoin block) and six block headers of consecutive Bitcoin blocks. Therefore, a blockchain is "readable" by other blockchains where contract-level verification is possible.

Currently, the Conflux confirmation rule is too complicated and computationally heavy. Even the light client version rule requires validation of at least tens of block headers and hence too expensive for contracts. The major reason is that those rules prioritize confirmation latency and user experience. However, for cross-chain interoperation use cases the simplicity of rules takes precedence over low confirmation latency.

Therefore, it would be better if Conflux supports a simple,  contract-friendly verification method, even though it likely results in a greater latency.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

The blocks with significant quality constitute a "slow chain" embedded in the tree-graph structure. More specifically, the timer chain blocks have quality 180 times of target quality, and hence the timer chain has expected block generation time ~90 seconds, which can be safely confirmed with Nakamoto consensus. Thus a Conflux SPV proof consisting of sufficiently many (say, seven or eight) chained timer blocks would be sufficient to provide confidence comparable to six-block confirmation in Bitcoin.

To implement efficient SPV proof with timer blocks, Conflux protocol should change in following two points:

- Every timer chain must reference the previous timer chain, in order to avoid inclusion of intermediate non-timer blocks in the proof. Since the block producer cannot tell if the mined block will be a timer block, the reference to latest timer block (in miner's local view) should be included in the header of every block. 
- For a target transaction not executed in a timer block, there should be a way to verify the status/receipt of that transaction without tracing back along non-timer blocks. This can possibly be solved by keeping receipts of executed transactions generated since the latest timer block. Requiring the target transaction to generate a specific state for the SPV proof is an alternative solution without changing Conflux protocol, but it is more expensive and less user friendly.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

There is no more details to be explained.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This proposal is not backwards compatible. It will require a hard fork.


## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
By adding a reference to the latest timer block and a merkle root of recent receipts, the security of current protocol is not influenced.

One risk is that a timer block may include incorrect receipts which may result in false SPV proofs. This risk can be easily eliminated by requiring that honest miners only reference timer blocks with correct receipts and otherwise the mined block is blamed and gets zero reward. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
