---
cip: TBD
title: Introduce Finality via Voting Among Staked
author: Fan Long <longfan@conflux-chain.org>
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

In this CIP we propose to introduce finality to the Conflux chain via voting
among staked CFX holders. This will increase the confidence of high value
transactions happening on Conflux in future and this will protect Conflux
against potential 51% attacks from PoW.

## Abstract

We propose to use a VRF to select active staked CFX holders to form a finality
committee. The committee is temporary and will rotate periodically every X
blocks/epochs. The committee will cast vote to determine the finality of a
block in the PoW chain. Once a block is final in the PoW chain, all future
miners will have to generate blocks under the chain regardless of the weight
composition of the tree graph. This will effectively protect Conflux chain from
long-range 51% attacks from PoW.

## Motivation

TBD

## Specification

TBD

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
