---
cip: 43
title: Introduce Finality via Voting Among Staked
author: Fan Long <longfan@conflux-chain.org> Chenxing Li <chenxing@conflux-chain.org>
discussions-to: XXX
status: Draft
type: Spec Breaking
created: 2021-01-05
requires (*optional): <CIP number(s)>
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

In this CIP, we propose introducing finality to the Conflux chain via voting among staked CFX holders. This will increase the confidence of high-value transactions happening on Conflux in the future and protect Conflux against potential 51% attacks from PoW.

## Abstract
We propose introducing a stand-alone PoS chain, which monitors the process of the PoW chain (the tree-graph structure) and reaches a consensus for the confirmed pivot blocks under the PoW confirmation rule. Once the PoS chain regards a pivot block as confirmed, all the PoW miners should follow it. This will effectively protect the Conflux chain from long-range 51% attacks from PoW.

The CFX holders can deposit to a specific contract to claim voting rights on the PoS chain. The PoS chain uses a VRF to select the nodes with voting rights. The committee is temporary and will rotate periodically every six hours. The terms are staggered like the US Senate. Approximately one-sixth of committee seats are up for election every one hour.

## Motivation

In the early stage of a public chain’s ecological development, the total amount of computing power is relatively insufficient. This results in a low attack cost to launch 51% attacks and steal the on-chain funds by double-spending. In the last year, Ethereum Classic, Grin and Verge sufferers 51% attack successively. To mitigate the attacker’s threat, Conflux introduces PoS finality, which forms a staking committee that recommends confirmed pivot blocks that the PoW nodes should follow.

## Specification

[PoS finality specification](https://github.com/ChenxingLi/pos-finalization/blob/main/pos-finality-spec.pdf)

## Rationale

TBD

## Backwards Compatibility

This CIP is not backwards compatible and would require a hard-fork to enable.

## Test Cases

TBD

## Implementation

TBD

## Security Considerations

TBD

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
