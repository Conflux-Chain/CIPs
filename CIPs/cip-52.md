---
CIP No.: 52
Title: Custom Eligibility Rules for Sponsorship
Author: Chenxing Li (@ChenxingLi)
Discussions: https://github.com/Conflux-Chain/CIPs/issues/54
Status: Draft
Type: Spec Breaking
Created: 2021-01-12
---

## Simple Summary
Add multiple rules in checking whether a transaction is sponsored. 

## Abstract
In the sponsor whitelist for a contract, each address corresponds to a 256-bit storage entry in a world-state. Currently, if the entry equals to 1, the corresponding address is sponsored. While if the entry equals to 0, the corresponding address is not sponsored. Besides, the zero address represents the rule for all the address. We plan to add more value type in this storage entry. 

## Motivation
As discussions in the Ethereum community, allowing a transaction issued by an abstract account enables multiple usages, e.g. allowing an address without ETH to send a transaction. The sponsorship mechanism in Conflux has supported such usages. However, due to the sponsorship mechanism provides limited rules in checking whether a transaction is sponsored, the contract developers face a dilemma in setting whitelist: sponsoring for all the addresses, which may incurs malicious usage; or sponsoring for only addresses registered in its contract, which may undermine the privacy and the decentralization of the Dapp. See [Account abstraction in Conflux](https://github.com/Conflux-Chain/CIPs/issues/54) for more details. 

In order to specify customized rule in the current Conflux network, a contract can simply sponsor for all the address and run a short EVM bytecode script at the beginning of executing this transaction. If the short script for verifying sender eligibility passed, the contract continue to execute the rest part, otherwise it revert the transaction. However, in case the transaction execution is reverted, Conflux only refunds at most 1/4 of unused gas. So it can not prevent the contract from DoS attack in sponsor balance.  

We plan to allow a contract to specify a short script for checking sponsorship eligibility, require all the miners running such script before packing block.  

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Parameters

We define the following parameters in this CIP.

- `SPONSOR_SCRIPT_GAS_LIMIT`: 3000

### Customized function

Each contract wants to specify the customized script should implements a function `__isSponsored__(bytes[] calldata) public view returns (bool)`. 

In the sponsor whitelist of a contract, if the zero address has value `2`, and the sender's address has value `0`, the full node should call the function `__isSponsored__` with gas limit `SPONSOR_SCRIPT_GAS_LIMIT` in packing transactions and on-chain execution. The return value decides if this transaction is sponsored. If the function fails, this transaction is regarded as an invalid transaction. In on-chain execution, the execution of `__isSponsored__` should consume gas as a normal function, no matter the transaction is sponsored or not. 

*Notice that the return value while packing transaction may not equal to the return value while on-chain execution.* 

The *static flag* is set while executing `__isSponsored__(bytes[] calldata)`, it means that the following opcode is forbidden:
- Ledger writing opcodes: `CREATE(0xf0)`, `CREATE2(0xf5)`, `SUICIDE(0xff)`, `SSTORE(0x55)`, `CALL` (with balance transfer), `CALLCODE` (with balance transfer).
- Log related opcodes: `LOG0(0xa0)`~`LOG4(0xa4)`.

The block related opcodes (`BLOCKHASH(0x40)`, `COINBASE(0x41)`, `TIMESTAMP(0x42)`, `NUMBER(0x43)`, `DIFFICULTY(0x44)`, `GASLIMIT(0x45)`) are also regarded as invalid here.

### Gas charge

Before executing the function `__isSponsored__(bytes[] calldata)`, we charge gas with `G_verylow + G_low × ⌈ calldata.len() ÷ 32 ⌉` first. It has the same gas cost as opcode `CALLDATACOPY(0x39)`. For a normal ERC-20 transfer transaction, it will cost only 12 gas. 


## Rationale

TBA.

## Backwards Compatibility

This CIP introduce additional rule in checking the eligibility to be sponsored. So it is spec breaking. 

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations


**Vulnerability 1:** An attacker can submit a lot of invalid CIP-52 transactions.

**Solution:** It is not too bad compared to the current transaction verification process. Let's consider the gas consumption in verifying the validity of a transaction. (*Note: In conflux, loading a normal account consumes about 800 gas, although we only charge 400 gas.*)

- Balance transfer to normal address: Signature Verification (6000) + Load sender account (800) + Basic ops (~20) = 6820 gas
- Calling contract transaction: Signature Verification (6000) + Load sender account (800) + Load contract account (1300) + Load two whitelist entries (400) + Basic ops (~100) = 8600 gas
- CIP-52 transaction: Signature Verification (6000) + Load sender account (800) + Load contract account (1300) + Customized Script (3000) = 10100 gas

Verifying CIP-52 transaction only costs 17% more gas than the current transactions.

**Vulnerability 2:** If an account senders a lot of transactions with the same nonce, although each of these transaction is valid before they are packed in blocks, only one of them is valid in on-chain execution. Fortunately, it is easy to detect such attack. However, if the attacker applies the same attack strategy by sending a lot of CIP-52 transactions, such attack will be hard to detect. 

**Solution:** If the miner guarantees that the contract has enough sponsor balance to pay for all these transactions, the sponsor will bear the loss. And it becomes the contract developer's duty to avoid such attack.  

**Vulnerability 3:** Before CIP-52, the CVM will not load contract code until the transaction is guaranteed valid. For the CIP-52 transaction, we must load contract node to verify the validity of a contract.  

**Solution:** Charge gas for loading contract bytecode, 200 gas for the first 32 bytes, and 100 gas for every additional 32 bytes. It doesn't matter because the entrance contracts of most Defi projects are just proxy smart contracts. For example, the entrance contract of Loopring only has 170 bytes, which will cost 700 gas here. Maybe we should increase `SPONSOR_SCRIPT_GAS_LIMIT` if we charge bytecode loading gas. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
