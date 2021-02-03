---
cip: 52
title: Customized Rule for Eligibility to be Sponsored
author: Chenxing Li (@ChenxingLi)
discussions-to: [Customized sponsorship check and account authorization](https://github.com/Conflux-Chain/CIPs/issues/54)
status: Draft
type: Spec Breaking
created: 2021-01-12
---

## Simple Summary
Add multiple rules in checking whether a transaction is sponsored. 

## Abstract
In the sponsor whitelist for a contract, each address corresponds to a 256-bit storage entry in a world-state. Currently, if the entry equals to 1, the corresponding address is sponsored. While if the entry equals to 0, the corresponding address is not sponsored. Besides, the zero address represents the rule for all the address. We plan to add more value type in this storage entry. 

## Motivation
As discussions in the Ethereum community, allowing a transaction issued by an abstract account enables multiple usages, e.g. allowing an address without ETH to send a transaction. The sponsorship mechanism in Conflux has supported such usages. However, due to the sponsorship mechanism provides limited rules in checking whether a transaction is sponsored, the contract developers face a dilemma in setting whitelist: sponsoring for all the addresses, which may incurs malicious usage; or sponsoring for only addresses registered in its attack, which may undermine the privacy and the decentralization of the Dapp. 

A better idea is allowing the contract to specify a short script to check whether a transaction is sponsored. Naively, a contract can simply sponsor for all the address and run a short script to decide whether continue to execute the rest part. However, Conflux only refunds at most 1/4 of unused gas. So it can not prevent the contract from DoS attack. 

We plan to allow a contract to specify a short script for checking sponsorship eligibility.  

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Parameters

We define the following parameters in this CIP.

- `SPONSOR_SCRIPT_GAS_LIMIT`: 3000

### Customized function

Each contract wants to specify the customized script should implements a function `confluxHandlerForCheckingSponsorship(bytes[4] func_sig) public view returns (bool)`. 

In the sponsor whitelist of a contract, if the zero address has value `2`, and the sender's address has value `0`, the full node should call the function `confluxHandlerForCheckingSponsorship` with gas limit `SPONSOR_SCRIPT_GAS_LIMIT` in packing transactions and on-chain execution. If the function returns true, this transaction is sponsored, otherwise, it is not sponsored. *Notice that the return value in packing transaction may not equal to the return value in on-chain execution.* 

In on-chain execution, the execution of `confluxHandlerForCheckingSponsorship` should consume gas as a normal function, no matter the transaction is sponsored or not. 

## Rationale

TBA.

## Backwards Compatibility

This CIP introduce additional rule in checking the eligibility to be sponsored. So it is spec breaking. 

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations

TBA.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
