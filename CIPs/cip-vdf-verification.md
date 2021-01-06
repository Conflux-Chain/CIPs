---
cip: TBD
title: Introduce VDF Verification Builtin
author: Fan Long <longfan@conflux-chain.com>
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

This CIP introduces new builtin/internal contract functions to enable
fast verification of VDF in Conflux. This is to support potential random
beacon development for gaming applications on Conflux.

## Abstract

VDF is the most promising solution for building randomness beacons on Conflux.
Directly using cEVM to code the verification of VDF may consume a lot of gas.
This CIP proposes to identify the VDF type that Conflux will promote to use and
then introduce builtin/internal contract functions to reduce the gas cost of
its verification.

## Motivation

TBD

## Specification

TBD

## Rationale

TBD

## Backwards Compatibility

TBD

## Test Cases

TBD

## Implementation

TBD

## Security Considerations
TBD

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
