---
cip: 86
title: Update Difficulty Adjustment Algorithm
author: Chenxing Li (@ChenxingLi)
status: Final
type: Spec Breaking
created: 2021-11-26
---


## Simple Summary
Reduce the period of difficulty adjustment and apply the simple moving average method. 

## Abstract
Reduce the period of difficulty adjustment from 5000 epochs to 250 epochs. To avoid sharp fluctuations in a short period, we apply the SMA (simple moving average) method over the difficulty adjustment. 

## Motivation
In the last several months, the Conflux consensus protocol suffered on-off mining. The block generation rate continued to fluctuate and reached a maximum of 0.15 seconds per block. This increased the forks in the tree-graph structure and increased the burden on the smart contract execution engine. To mitigate this problem, we need to reduce the difficulty adjustment period to react more timely to computing power change. 

## Specification
### The old behavior 

For the block whose block height equals `5000k`, where `k` is an integer, we compute parameters `n` and `T` as follows:

- `n`: The number of blocks in the last 5000 epochs.
- `T`: The time elapsed in the last 5000 epochs (in terms of seconds). (See Conflux Protocol Specification for more details in computing `T`)

Let `d'` be the difficulty before adjustment, then the estimated computing power in the last 5000 epochs is `p = n * d' / T` (hashes/second). In order to set the block generation rate to 2 blocks/second, the target difficulty `d` is defined as `d = p / 2 = n * d' / T / 2`.

So the difficulty after adjustment  `D`  will be set to `d`. Specially, if `d > 1.5 d'`, `D` is set to `1.5 d'`. If `d < 0.5d'`, `D` is set to `0.5 d'`.

### The new behavior

For the block whose block height equals `250k`, where `k` is an integer, we compute parameters `n` and `T` as follows:

- `n`: The number of blocks in the last 250 epochs.
- `T`: The time elapsed in the last 250 epochs (in terms of seconds). 

Let `d'` be the difficulty before adjustment. Similarly, the target difficulty `d` is defined as `d = n * d' / T / 2`.

Instead of setting `d` as the adjusted difficulty, we apply a simple average method here. The difficulty of the next 250 epochs will be set to `0.8 d' + 0.2 d`. Specially, if `0.8 d' + 0.2 d > 1.5 d'`, `D` is set to `1.5 d'`. 

## Rationale
The root cause of computing power fluctuation is that the difficulty adjustment algorithm predicts the computing power for **the next several epochs** from the estimated computing power in **the last several epochs**. So if the computing power continues to change, the algorithm will always result in a wrong prediction. 

A complete solution is to reduce the base reward if the actual computing power is much larger than predicted. So the miners will be disincentivized to switch in and out. However, this solution involves a change in the economic model and needs more discussion. So here we propose a simple solution, which reduces the period of adjustment and makes the computing power in the hour window stable. 

## Backward Compatibility

This change is spec-breaking. It should be activated at a block height that divides 5000.

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations


A short difficulty adjustment period may make the estimation for parameter `T` inaccurate. But in the normal case, the error is less than 5%. The attacker may manipulate the results of `T` by some dedicated strategies, but the only effect is to slow down the block generation rate. Fortunately, the lower the block generation rate, the less capability to manipulate `T`.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
