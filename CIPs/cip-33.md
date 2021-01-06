---
cip: 33
title: Transaction fee pricing mechanism for Tephys
author: Justin Pan (@zimengpan)
discussions-to: <URL>
status: Draft
type: Backward Compatible
created: 2020-10-15
---
  
## Simple Summary
A transaction fee pricing mechanism with algorithmically-determined per-block base network fee that allows passive bidding on gas price.

## Abstract
In each block, the base network fee per gas (base fee) is a fixed number and is burned instead of received by miners.

Between blocks, the base fee is adjusted solely based on the gas used in the parent block and gas target of the parent block. The algorithm should behave in a way that increases the fee when blocks surge above the gas target and decrease when blocks drop below the gas target.

Transactions should specify the maximum fee per gas (fee cap), and the maximum fee per gas for incentivizing miners (miner gratuity).

Transactions always pay the base fee of the block it is included in, and then pay the miner gratuity, as long as the combined amount of the two fees does not exceed the fee cap.

## Motivation
The current pricing model is largely derived from First Price Auctions. This causes three major issues:
1. Inefficiency and unpredictability in price offering
2. High incentive for miners to manipulate transaction fees, such as selfish mining attacks and more
3. Inflation due to ever-increasing supply in circulation

From miner's perspective, the new implementation changes their income structure from gas fee to miner gratuity plus block rewards. When base fee is burned, it allows CFX to directly harvest the liquidity and storage in transactions.

From user's perspective, since the base fee changes are preditable and universal, this allows wallets to auto-set the gas fees (base fee + small gratuity) for most users that guarantee tansaction confirmation even when network is congested.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platform ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->
See reference implementation in [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559). Need localization for Conflux core.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
This proposal is inspired by [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559).

Besides all the reasons stated in 1559, we've also observed fanatic discussions about CFX and FC recently. Without a comprehensive and fundamental mechanism to exhaust the gas out of circulation, CFX will face ever-increasing supply-side pressure.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
See Go-Ethereum implementation in [Vulcanize implementation](https://github.com/vulcanize/go-ethereum-EIP1559). Need localization for Conflux core.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
### Transaction Ordering
Instead of auctioning on miner fees to get included, the transactions with the same miner gratuity should be sorted by the time the transaction was received, forming a default execution order; this also protects the network from spamming attacks where the attacker throws transactions into the pending pool to occupy a favorable position. The strategy of the miners should still prefer transactions with higher gratuity, only for better earnings instead of active biddings.

### Mining Empty Blocks
It is possible that miners will mine empty blocks until the base fee is very low and then proceed to mine half full blocks, reverting the pricing mechansim to sorting transactions by the miner gratuity. While this attack is possible, it is not a particularly stable equilibrium as long as mining is decentralized. Any defector from this strategy will be more profitable than a miner participating in the attack for as long as the attack continues (even after the base fee reached 0). Since any miner can anonymously defect from a cartel, and there is no way to prove that a particular miner defected, the only feasible way to execute this attack would be to control 50% or more of hashing power. If an attacker had exectly 50% of hashing power, they would make no money from miner bribe while defectors would make double the money from bribes. For an attacker to turn a profit, they need to have some amount over 50% hashing power, which means they can alternatively execute double spend attacks or simply ignore any other miners which is a far more profitable strategy.

Should a miner attempt to execute this attack, we can simply increase the elasticity multiplier (currently 2x) which requires they have even more hashing power available before the attack can even be theoretically profitable against defectors.

### Unpredicable CFX Supply
By burning the base fee, one can no longer guarantee a fixed token supply. This could result in economic instabality as the long term supply of CFX will no longer be constant over time. While a valid concern, it is difficult to quantify how much of an impact this will have. If more is burned on base fee than is generated in mining rewards then CFX will be deflationary and if more is generated in mining rewards than is burned then CFX will be inflationary. Since we cannot control user demand for block space, we cannot assert at the moment whether CFX will end up inflationary or deflationary, so this change causes the core developers to lose some control over Conflux’s long term monetary policy.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
