---
CIP No.: 43
Title: Introduce Finality Through Staking Vote
Author: Fan Long <longfan@confluxnetwork.org> Chenxing Li <chenxing@confluxnetwork.org>
Status: Final
Type: Spec Breaking
Created: 2021-01-05
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

In this CIP, we propose introducing finality to the Conflux chain via voting among staked CFX holders. This will increase the confidence of high-value transactions happening on Conflux in the future and protect Conflux against potential 51% attacks from PoW.

## Abstract
We propose introducing a stand-alone PoS chain, which monitors the process of the PoW chain (the tree-graph structure) and reaches a consensus for the confirmed pivot blocks under the PoW confirmation rule. Once the PoS chain regards a pivot block as confirmed, all the PoW miners should follow it. This will effectively protect the Conflux chain from long-range 51% attacks from PoW.

The CFX holders can deposit to a specific contract to claim voting rights on the PoS chain. The PoS chain uses a VRF to select the nodes with voting rights. The committee is temporary and will rotate periodically every six hours. The terms are staggered like the US Senate. Approximately one-sixth of committee seats are up for election every one hour.

## Motivation

In the early stage of a public chain’s ecological development, the total amount of computing power is relatively insufficient. This results in a low attack cost to launch 51% attacks and steal the on-chain funds by double-spending. In the last year, Ethereum Classic, Grin and Verge sufferers 51% attack successively [[1](https://www.coindesk.com/markets/2020/08/29/ethereum-classic-hit-by-third-51-attack-in-a-month/), [2](https://www.coindesk.com/markets/2020/08/29/ethereum-classic-hit-by-third-51-attack-in-a-month/), [3](https://www.coindesk.com/markets/2020/11/08/privacy-coin-grin-is-victim-of-51-attack/), [4](https://news.bitcoin.com/privacy-coin-verge-third-51-attack-200-days-xvg-transactions-erased/)]. To mitigate the attacker’s threat, Conflux introduces PoS finality, which forms a staking committee that recommends confirmed pivot blocks that the PoW nodes should follow.

## Specification

[PoS finality specification](https://github.com/ChenxingLi/pos-finalization/blob/main/pos-finality-spec.pdf)

(The link is an old version. If there are any inconsistency between this CIP and the linked document, this CIP shall prevail.)

## Rationale

TBD

## Backwards Compatibility

This CIP is not backwards compatible and would require a hard-fork to enable.

## Test Cases

TBD

## Implementation

TBD

## Security Considerations

We all know that a PoW consensus protocol can provide security when the attacker controls <50% computing power. But what is the case after activating CIP-43. We list some senarios here. 

- **51% Attack:** If the attacker controls >51% computing power and the PoS chain works well, the pivot blocks that
have been decided by the PoS chain are still safe. However, the PoW chain may lose liveness because the PoS nodes
cannot confirm any new blocks. If the attacker disappears, the consensus protocol can execute as usual.
- **34% Staking Attack:** If the attacker controls >34% PoS staking tokens, the PoS chain may lose liveness. Then
the Conflux chain runs as if CIP-43 is not activated. If the attacker disappears, the consensus protocol can execute as usual. But a known delicated attack puts the PoW chain at risk of diverge. This may be resolved later. 
- **67% Staking Attack:**  If the attacker controls >67% committee members, it can generate conflict PoS blocks. In this case, the PoW chain will diverge, even if the attacker disappears.
- **Long-range attack:** Different from the 84% staking attack, in a long-range attack, a PoS node becomes malicious
not because it intended to break the consensus, but because it lost private keys. So we assume the attacker can only
control the majority committee in a very early stage. If the attacker controls >51% computing power and can revert
the PoW chain from the early stage, a double-spending attack happens. Otherwise, the consensus protocol can execute
as usual.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
