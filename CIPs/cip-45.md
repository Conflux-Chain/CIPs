---
cip: 45
title: Introduce Flexible Account Authorization Methods
author: Guang Yang (@Regulusyg)
discussions-to: [Customized sponsorship check and account authorization](https://github.com/Conflux-Chain/CIPs/issues/54)
status: Draft
type: Spec Breaking
created: 2021-01-06
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This CIP introduces flexible account authorization methods in Conflux. This is to support Conflux accounts controlled by rules other than ECDSA with SECP-256k1. 

## Abstract

Advanced and flexible authorization rules are important for better management of accounts. 

For example, BLS signature with curve BLS12-381 (as specified in https://tools.ietf.org/html/draft-irtf-cfrg-bls-signature-04, also included in eth2.0-specs) supports efficient implementation of threshold-signature (m-of-n multisig) and aggregation of signatures, and DKIM (Domain Key Identified Mail) lowers the barrier to own a blockchain account by binding the account to an email address. 

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
