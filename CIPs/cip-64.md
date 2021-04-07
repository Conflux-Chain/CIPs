---
cip: 64
title: Get current epoch number through internal contract
author: Péter Garamvölgyi <@Thegaram>
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/65
status: Draft
type: Spec Breaking
created: 2021-03-08
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->


## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->

Currently, transactions on Conflux have no direct access to the number of the epoch they are executed in. To maintain EVM compatibility, this CIP introduces a new internal contract that makes this information available to contracts.


## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

A common technique in dapp development is to store the block number corresponding to an event in a contract's storage and subsequently use this information to query the contract. For instance, one might want to start querying logs starting from the contract's creation. However, the query APIs of Conflux work with epoch numbers, not block numbers, which renders this technique infeasible. This CIP introduces a new internal contract that makes the execution epoch number available to contracts.


## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

There are two common use cases for using block numbers (`block.number`) in smart contracts.

The first use case is timing. The growth rate of block numbers is relatively steady and hard to influence, which makes them suitable for applications like time locks.

The second use case is querying. For instance, a contract might store its creation block number and clients might read this to know from which block they need to start querying the contract's events.

This latter use case is not feasible on Conflux as our query APIs (RPC) only support querying by epoch number, not by block number.


## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

Starting from epoch XXX, a new internal contract is available at the address `0x0888000000000000000000000000000000000003` (`cfx:type.builtin:aaejuaaaaaaaaaaaaaaaaaaaaaaaaaaaapx8thaezf`). This contract has the following interface.


```solidity
pragma solidity >=0.4.15;

contract Context {
    /*** Query Functions ***/
    /**
     * @dev get the current epoch number
     */
    function epochNumber() public view returns (uint64) {}
}
```

When a transaction's execution triggers a call to `epochNumber()`, the internal contract implementation must return the current epoch number from the transaction's execution context.

Analogous to the `NUMBER` opcode, the gas cost of this internal contract call should be `2`.


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

Instead of adding a new opcode, this functionality is implemented as an internal contract to maintain compatibility with Ethereum's EVM specification. This means that we do not need to maintain our own fork of the Solidity compiler and similar developer tools.


## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

As this proposal introduces a new internal contract, it is not backwards compatible.


## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->

TBA.


## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

TBA.


## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

This proposal brings no security issues.


## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
