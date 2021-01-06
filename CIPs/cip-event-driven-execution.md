---
cip: XXX
title: Support Event Driven Execution Model
author: Fan Long <longfan@conflux-chain.com>
discussions-to: XXX
status: Draft
type: Spec Breaking
created: 2020-08-15
requires (*optional): <CIP number(s)>
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This CIP introduces new opcodes to enable event-driven execution model in EVM.

## Abstract

This CIP introduces a set of novel new opcodes to enhance the EVM in Conflux
(cEVM). The enhanced cEVM  will enable developers to build fully autonomous
smart contracts. It introduces another way for contracts to interact with each
other. Contracts in the enhanced cEVM can emit signal events, on which other
contracts can listen. Once an event is triggered, corresponding handler
functions are automatically executed as signal transactions. This enhancement
can significantly simplify the execution flow of many decentralized
applications espeically DeFi applications. It can also avoid security risks
such as front-run attacks by miners.

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
