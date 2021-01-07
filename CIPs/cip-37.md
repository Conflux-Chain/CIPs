---
cip: 37
title: Introduce Base58Check Addresses
author: bytelaw, Péter Garamvölgyi <peter@conflux-chain.com>
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/37
status: Draft
type: Spec Breaking
created: 2021-01-07
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This CIP introduces a new address format to help users distinguish between Conflux and Ethereum addresses.

## Abstract

Apart from the regular type-prefixed base64 hex addresses Conflux uses today, we introduce a new base58-encoded address format. Keys of the new format are derived from the public key directly. They have an easy-to-remember fixed prefix (e.g. `"cfx"`), a type prefix, and a checksum. Using base58check encoding helps us avoid and detect typos and makes it easy to disambiguate Conflux addresses from addresses on other blockchains, thus reducing the risk of loss of funds.

The address of a user account could look like this: `cfx1rituYSmbeEDxXpDVF7ordH3q7grYFo`.

## Motivation

Currently, Conflux and Ethereum addresses are very similar, in many cases they are cross-compatible, i.e. some valid Ethereum addresses are valid Conflux addresses and vice versa. This has led many users to lose their funds when transferring them to a Conflux address on Ethereum, particularly during cross-chain operations. A new key type that is incompatible with Ethereum would allow tools like Metamask to prevent such erroneous transactions from being submitted.

## Specification

TBD

## Rationale

TBD

## Backwards Compatibility

This change requires a protocol upgrade so that Conflux nodes can interpret and process new addresses.

This change also requires updating various tools (wallets, IDEs, etc.) in the Conflux ecosystem.

## Test Cases

TBD

## Implementation

TBD

## Security Considerations

TBD

## References

Bitcoin Wiki: Base58Check encoding \[[link](https://en.bitcoin.it/wiki/Base58Check_encoding)\]

IETF: The Base58 Encoding Scheme \[[link](https://tools.ietf.org/id/draft-msporny-base58-01.html)\]

EIPs #77: Safer Ethereum Address Format \[[link](https://github.com/ethereum/eips/issues/77)\]

Tron Documentation: 3.4 Address Format \[[link](https://developers.tron.network/docs/account#address-format)\]

Tezos Base58 Encoding with Prefix (eztz codebase) \[[link](https://github.com/TezTech/eztz/blob/master/src/main.js#L15)\]

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
