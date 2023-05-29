---
CIP No.: 76
Title: Remove VM-Related Constraints in Syncing Blocks
Author: Chenxing Li (<lylcx2007@gmail.com>)
Status: Final
Type: Spec Breaking
Created: 2021-07-02
---

## Simple Summary
We should remove VM-related constraints in syncing blocks, like requiring the transactions to have enough gas limit.

## Abstract
Currently, before adding new blocks to synchronization graphs, we checked several constraints. One of them requires the transaction gas limit to exceed a minimum threshold. However, this threshold relies on the gas plan of the virtual machine, which may change in future upgrades. This CIP proposes to remove VM-related constraints in syncing blocks and avoid introducing similar rules in the future.  

## Motivation
Decoupling the consensus component with the execution component is vital to the further upgrade plans. The consensus protocol should transparent to the execution component. 

However, if the synchronization graph reads VM-related parameters in checking the validity of a received block, any change in VM level may cause a hard fork in the consensus protocol.  

The constraint for the transaction gas limit cannot prevent a malicious miner from fulfilling a block with useless transactions because we don't check if the sender has enough balance to pay for the gas fee. The malicious miner can pack a lot of transactions whose sender has zero balance. 

## Specification
After a given epoch height, the consensus protocol no longer checks the block heights for transactions. 

## Rationale
When applying the basic checks for a new block, we don't know about block number and epoch size. The only way to activate this CIP is at a given block height (epoch number). 

## Backwards Compatibility
This CIP is spec-breaking.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
As described in the motivation part, the current implementation can not prevent a malicious miner fullfiling useless transactions in a block. So removing a contraint will not make it worse. 

But more security discussions are necessary for this change. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
