---
CIP No.: 46
Title: Introduce Conflux Domain Name Service
Author: Guang Yang (@Regulusyg)
Status: Draft
Type: Backward Compatible
Created: 2021-01-06
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This draft CIP introduces the Conflux Name Service, migrating from [EIP137](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-137.md).

## Abstract

This draft CIP describes the details of the Conflux Name Service, a proposed protocol and ABI definition that provides flexible resolution of short, human-readable names to service and resource identifiers. This permits users and developers to refer to human-readable and easy to remember names, and permits those names to be updated as necessary when the underlying resource (contract, content-addressed data, etc) changes.

The goal of domain names is to provide stable, human-readable identifiers that can be used to specify network resources. In this way, users can enter a memorable string, such as 'vitalik.wallet' or '[www.mysite.swarm](http://www.mysite.swarm/)', and be directed to the appropriate resource. The mapping between names and resources may change over time, so a user may change wallets, a website may change hosts, or a swarm document may be updated to a new version, without the domain name changing. Further, a domain need not specify a single resource; different record types allow the same domain to reference different resources. For instance, a browser may resolve 'mysite.swarm' to the IP address of its server by fetching its A (address) record, while a mail client may resolve the same address to a mail server by fetching its MX (mail exchanger) record.

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
