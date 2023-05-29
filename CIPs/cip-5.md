---
CIP No.: 5
Title: Fix MPT Key Encoding Corner Case
Author: Thegaram <th307q@gmail.com>
Discussions: https://github.com/Conflux-Chain/conflux-rust/issues/1693
Status: Final
Type: Spec Breaking
Created: 2020-07-22
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->

Simple Merkle Patricia Tries (or just *MPTs*) are used to calculate the `transactions_root` and `deferred_receipts_root` header fields and to provide the corresponding light node proofs. In the special case where a block contains **exactly** 256 transactions, the current implementation erroneously encodes MPT keys using two bytes instead of one.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

A block's transactions are stored in an MPT where they key is the block index and the value is the transaction hash. The root of this MPT is stored in the `transactions_root` header field. A similar approach is used for receipts.

Normally, MPT keys should be encoded into the smallest-length byte array that can accommodate all keys, i.e. for 0-256 transactions keys are encoded as 1 byte, for 257-65536 as 2 bytes, etc.

However, an off-by-one bug in the implementation will cause blocks with **exactly** 256 transactions (largest transaction index 255) encode the keys into 2 bytes instead of 1. (This also applies to 2^16, 2^24, ... items but Conflux does not allow that many transactions in one block.) This makes key encoding inconsistent and counter-intutitive.

As the correctness of the `transaction_root` is a preqrequisite of block validity (see *3.4.3 Well-formedness* and *3.4.4 Block Header Validity* in the [spec](https://confluxnetwork.org/static/Conflux_Protocol_Specification_20200714.pdf)), changing this is a breaking change. Please note that the intended behavior is not reflected in the current version of the specification.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

The main motivation for fixing this issue is to restore the intended behavior.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

An example fix can be found in [conflux-rust#1695](https://github.com/Conflux-Chain/conflux-rust/pull/1695).

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

The fix to handle the corner case correctly is straightforward. The main question is how to deploy it. We can either do this as part of a hard fork or deploy as part of phase 3.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

If the fix is adopted by only a portion of the nodes, a block with exactly 256 transactions will result in a pivot chain fork.

We will enable this change in a hard fork of Oceanus.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->

Please refer to the unit tests in [conflux-rust#1695](https://github.com/Conflux-Chain/conflux-rust/pull/1695).

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

See [conflux-rust#1695](https://github.com/Conflux-Chain/conflux-rust/pull/1695).

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

Blocks with exactly 256 transactions are rare. It is tempting to just slip this change in as it is unlikely to break anything. However, as long as the fix is only adopted by a portion of all nodes, a malicious node can very easily fork the pivot chain by simply mining a block with 256 transactions.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
