# Conflux Improvement Proposals
Conflux Improvement Proposals (CIPs) describe standards for the Conflux platform, including core protocol specifications, client APIs, and contract standards.

## Contributing

1. Review [CIP-1](https://github.com/Conflux-Chain/CIPs/blob/master/CIPs/cip-1.md).
2. Fork the repository by clicking "Fork" in the top right.
3. Add your CIP to your fork of the repository. There is a [template CIP here](cip-template.md).
4. Submit a Pull Request to Conflux's [CIPs repository](https://github.com/Conflux-Chain/CIPs).

Your initial PR should be a draft of the final CIP, and it must comply with the formatting rules set by the build, including correct metadata in the header.

Your PR ID may be utilized as a provisional CIP ID at the submitter's discretion, obviating the necessity for editorial approval. An editor will manually review the first PR for a new CIP and assign a definitive CIP ID. Typically, editors do not alter CIP ID. However, exceptions do occur, as demonstrated by [CIP-1820](CIPs/cip-1820.md). In the event that the chosen PR ID is occupied by an existing CIP, the submitter is obliged to await the editor's allocation of a new CIP ID. CIPs submitted via issues are considered invalid.

Considering the current size of the Conflux community, it's not practical for community members to follow up with technically complex CIPs. For such CIPs, the main discussions are held within the dev team, and the outcomes are promptly updated in the CIP documents. For CIPs that may have a significant impact, triggering large-scale discussions, you should start a topic in the GitHub issue or Conflux forum for more in-depth discussions. These CIPs must include a Discussion header with a link to a discussion forum or open GitHub issue where the CIP can be discussed as a whole.

If your CIP requires images, the image files should be included in a subdirectory of the `assets` folder for that CIP as follows: `assets/CIP-N` (where **N** is to be replaced with the CIP number). When linking to an image in the CIP, use relative links such as `../assets/CIP-1/image.png`.

If your CIP depends on other CIPs that haven't been activated on the mainnet, include a "Required CIPs" field in the header. You don't need to list prerequisites that have been activated on the mainnet.

To facilitate communication, you can provide multilingual versions for CIP, such as [CIP-109](CIPs/cip-109.md). However, an English version is essential. In case of discrepancies between different language versions, the English one will serve as the definitive reference.

Once your first PR is merged, we have a bot that helps out by automatically merging PRs to draft CIPs. For this to work, it has to be able to tell that you own the draft being edited. Make sure that the 'author' line of your CIP contains either your GitHub username or your email address inside. If you use your email address, that address must be the one publicly shown on [your GitHub profile](https://github.com/settings/profile).

When you believe your CIP is mature and ready to progress past the draft phase, you should ask to have your issue added to the agenda of an upcoming All Core Devs meeting, where it can be discussed for inclusion in a future hard fork. If implementers agree to include it, the CIP editors will update the state of your CIP to **Accepted**.

# CIP Status Terms

- **Draft** - a CIP that is undergoing rapid iteration and changes.
- **Last Call** - a CIP that is done with its initial iteration and ready for review by a wide audience.
- **Accepted** - a CIP that has been in **Last Call** for at least 2 weeks and any technical changes that were requested have been addressed by the author. The process for Core Devs to decide whether to encode a CIP into their clients as part of a hard fork is not part of the CIP process. If such a decision is made, the CIP will move to **Final**.
- **Final** - a CIP that the Core Devs have decided to implement and release in a future hard fork or has already been released in a hard fork.

# CIP List

## Highlighted CIPs

These CIPs significantly influence the economic and governance models of Conflux, as well as its various features.

| CIP Number | Title | Activation Hardfork |
| :-----: | :----- | :-----: |
| [37](CIPs/cip-37.md) | Introduce Base32 Checksum Addresses | (Not Spec Breaking) |
| [40](CIPs/cip-40.md) | Reduce Block Base Reward to 2 CFX | Tanzanite (V1.1) |
| [43](CIPs/cip-43.md) | Introduce Finality Through Staking Vote | Hydra (V2.0) |
| [90](CIPs/cip-90.md) | Introduce a Fully EVM-Compatible Space | Hydra (V2.0) |
| [94](CIPs/cip-94.md) | On-Chain DAO Vote for Chain Parameters | V2.1 |
| [107](CIPs/cip-107.md) | DAO-Adjustable Burn of Storage Collateral | V2.3 |
| [1559](CIPs/cip-1559.md) | Fee market change for Conflux | V2.4 |
| [7702](CIPs/cip-7702.md) | Set Code for EOA | V3.0 (Pending) |
| [645](CIPs/cip-645.md) | Align Conflux eSpace Behavior with EVM | V3.0 (Pending) |

These CIPs are primarily aimed at following EIPs to maintain compatibility with Ethereum. The specifications are highly align with Ethereum.

| CIP Number | Title | Related EIP Number |
| :-----: | :----- | :-----: |
| [23](CIPs/cip-23.md) | Hashing and Signing Typed Structured Data | [EIP-712](https://eips.ethereum.org/EIPS/eip-712)
| [92](CIPs/cip-92.md) | Enable Blake2F Builtin Function | [EIP-152](https://eips.ethereum.org/EIPS/eip-152)
| [119](CIPs/cip-119.md) | `PUSH0 (0x5f)` Instruction | [EIP-3855](https://eips.ethereum.org/EIPS/eip-3855)
| [142](CIPs/cip-142.md) | Transient Storage Opcodes | [EIP-1153](https://eips.ethereum.org/EIPS/eip-1153)
| [143](CIPs/cip-143.md) | `MCOPY (0x5e)` Opcode for Efficient Memory Copy  | [EIP-5656](https://eips.ethereum.org/EIPS/eip-5656)
| [144](CIPs/cip-144.md) | Point Evaluation Precompile from EIP-4844  | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)
| [150](CIPs/cip-150.md) | Reject New Contract Code Starting with the 0xEF Byte | [EIP-3541](https://eips.ethereum.org/EIPS/eip-3541)
| [151](CIPs/cip-151.md) | SELFDESTRUCT only in Same Transaction | [EIP-6780](https://eips.ethereum.org/EIPS/eip-6780)
| [152](CIPs/cip-152.md) | Reject Transactions from Senders with Deployed Code | [EIP-3607](https://eips.ethereum.org/EIPS/eip-3607)
| [1820](CIPs/cip-1820.md) | Migrate ERC1820 to Conflux | [EIP-1820](https://eips.ethereum.org/EIPS/eip-1820)
| [7702](CIPs/cip-7702.md) | Set Code for EOA | [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702)
| [165](CIPs/cip-165.md) | Precompile for BLS12-381 Curve Operations | [EIP-2537](https://eips.ethereum.org/EIPS/eip-2537)

The following CRCs do not have dedicated documentation; they are identical to the corresponding ERCs.

| CRC Number | Related ERC Number |
| :-----: | :-----: |
| 20 | [ERC-20](https://eips.ethereum.org/EIPS/eip-20) |
| 721 | [ERC-721](https://eips.ethereum.org/EIPS/eip-721) |
| 1155 | [ERC-1155](https://eips.ethereum.org/EIPS/eip-1155) |


## Spec Breaking CIPs

### Ongoing

These CIPs are either in the midst of active development or have been fully developed, currently awaiting discussions on upgrade plans.

| CIP Number | Title | Status |
| :--------: | :---- | :----: |
| [150](CIPs/cip-150.md) | Reject New Contract Code starting with the 0xEF byte | Final |
| [151](CIPs/cip-151.md) | SELFDESTRUCT only in Same Transaction | Final |
| [152](CIPs/cip-152.md) | Reject Transactions from Senders with Deployed Code | Final |
| [154](CIPs/cip-154.md) | Fix Inconsistent Implementation of TLOAD | Final |
| [156](CIPs/cip-156.md) | Change the PoS Malicious Penalty From Stake Forfeiting to Stake Locking | Final |
| [645](CIPs/cip-645.md) | Align Conflux eSpace Behavior with EVM | Final |
| [7702](CIPs/cip-7702.md) | Set Code for EOA | Final |
| [165](CIPs/cip-165.md) | Precompile for BLS12-381 Curve Operations | Final |


### Activated

These CIPs have successfully undergone upgrades on the mainnet.

| CIP Number | Title | Activation Hardfork |
| :-----: | :----- | :-----: |
| [3](CIPs/cip-3.md) | Octopus Proof-of-Work Mining Algorithm | Tethys (V1.0) |
| [5](CIPs/cip-5.md) | Fix MPT Key Encoding Corner Case | Tethys (V1.0) |
| [8](CIPs/cip-8.md) | Delay Code Collateral Settlement | Tethys (V1.0) |
| [10](CIPs/cip-10.md) | Mining Reward Finalization | Tethys (V1.0) |
| [12](CIPs/cip-12.md) | Allow Non-Existent Sponsor for Collateral | Tethys (V1.0) |
| [13](CIPs/cip-13.md) | Employ Big-Endian MPT Keys | Tethys (V1.0) |
| [16](CIPs/cip-16.md) | Consolidate Contract Destruction Logic | Tethys (V1.0) |
| [21](CIPs/cip-21.md) | Minimum Storage Commitment Per Epoch | Tethys (V1.0) |
| [22](CIPs/cip-22.md) | Add Interfaces for Internal Contracts | Tethys (V1.0) |
| [26](CIPs/cip-26.md) | Use Pivot Block Timestamp for Opcode TIMESTAMP | Tethys (V1.0) |
| [27](CIPs/cip-27.md) | Delete Whitelist Keys at Contract Removal | Tethys (V1.0) |
| [31](CIPs/cip-31.md) | Genesis Block Contract: Create2Factory | Tethys (V1.0) |
| [39](CIPs/cip-39.md) | Use Custom Field for Incompatible Upgrade | Tanzanite (V1.1) |
| [40](CIPs/cip-40.md) | Reduce Block Base Reward to 2 CFX | Tanzanite (V1.1) |
| [43](CIPs/cip-43.md) | Introduce Finality Through Staking Vote | Hydra (V2.0) |
| [62](CIPs/cip-62.md) | Enable EC-Related Builtin Contracts | Hydra (V2.0) |
| [64](CIPs/cip-64.md) | Get Current Epoch Number via Internal Contract | Hydra (V2.0) |
| [71](CIPs/cip-71.md) | Disable Anti-Reentrancy | Hydra (V2.0) |
| [76](CIPs/cip-76.md) | Remove VM-Related Constraints in Syncing Blocks | Hydra (V2.0) |
| [78](CIPs/cip-78.md) | Correct `is_sponsored` Fields in Receipt | Hydra (V2.0) |
| [86](CIPs/cip-86.md) | Update Difficulty Adjustment Algorithm | Hydra (V2.0) |
| [90](CIPs/cip-90.md) | Introduce a Fully EVM-Compatible Space | Hydra (V2.0) |
| [92](CIPs/cip-92.md) | Enable Blake2F Builtin Function | Hydra (V2.0) |
| [94](CIPs/cip-94.md) | On-Chain DAO Vote for Chain Parameters | V2.1 |
| [97](CIPs/cip-97.md) | Clear Staking Lists | V2.1 |
| [98](CIPs/cip-98.md) | Fix BLOCKHASH Opcode Bug in eSpace | V2.1 |
| [99](CIPs/cip-99.md) | Extend Non-Voting Grace and Shorten Unlock Time | V2.1 |
| [105](CIPs/cip-105.md) | Minimal DAO Vote Count Based on PoS Staking | V2.1 |
| [107](CIPs/cip-107.md) | DAO-Adjustable Burn of Storage Collateral | V2.3 |
| [112](CIPs/cip-112.md) | Fix Block Headers `custom` Field Serde | V2.3 |
| [113](CIPs/cip-113.md) | Accelerate Up PoS Finalization | V2.3 |
| [118](CIPs/cip-118.md) | Query Unused Storage Points in Internal Contract | V2.3 |
| [119](CIPs/cip-119.md) | `PUSH0 (0x5f)` Instruction | V2.3 |
| [130](CIPs/cip-130.md) | Aligning Gas Limit with Transaction Size | V2.4 |
| [131](CIPs/cip-131.md) | Retain Whitelist on Contract Deletion | V2.4 |
| [132](CIPs/cip-132.md) | Fix Static Context Check for Internal Contracts | V2.4 |
| [133](CIPs/cip-133.md) | Enhanced Block Hash Query | V2.4 |
| [136](CIPs/cip-136.md) | Increase PoS Retire Period | V2.4 |
| [137](CIPs/cip-137.md) | Base Fee Sharing in CIP-1559 | V2.4 |
| [141](CIPs/cip-141.md) | Disable Subroutine Opcodes | V2.4 |
| [142](CIPs/cip-142.md) | Transient Storage Opcodes | V2.4 |
| [143](CIPs/cip-143.md) | `MCOPY (0x5e)` Opcode for Efficient Memory Copy | V2.4 |
| [144](CIPs/cip-144.md) | Point Evaluation Precompile from EIP-4844 | V2.4 |
| [145](CIPs/cip-145.md) | Fix Receipts upon `NotEnoughBalance` Error | V2.4 |
| [1559](CIPs/cip-1559.md) | Fee market change for Conflux | V2.4 |

### List of Hardforks

| Hardfork Version Number | Main Features | Time Nodes |
|-------------------------|---------------|------------|
| Tethys (V1.0) | Mainnet Activation | All features activated at genesis block (2020-10-29) |
| Tanzanite (V1.1) | Reducing base reward to alleviate selling pressure | All features activated at epoch number 3,615,000 (2020-11-18) |
| Hydra (V2.0) | Introduction of PoS hybrid consensus and EVM-compatible Space | Features activated based on epoch number: activated at epoch number 36,935,000 (2022-02-21)<br><br>Features activated based on block number: activated at block number 92,060,600 (2022-02-22)<br><br>First round of PoS consensus committee election ends at block number 92,751,800 (2022-02-26)<br><br>PoS consensus starts at epoch number 37,400,000 (2022-02-27) |
| V2.1 | Introduction of DAO voting mechanism | Features activated based on epoch number: activated at epoch number 56,800,000 (2022-10-15)<br><br>Features activated based on block number: activated at block number 133,800,000 (2022-10-25) |
| V2.2 | Emergency bug fix for PoS Key security | Activated at block number 137,740,000 (2022-11-17) |
| V2.3 | Introduction of storage collateral token burning mechanism | Features activated based on epoch number: activated at epoch number 79,050,000 (2023-09-10)<br><br>Features activated based on block number: activated at block number 188,900,000 (2023-09-09) |
| V2.4 | Introduction of EIP-1559 gas fee burning mechanism | Features activated based on epoch number: activated at epoch number 101,900,000 (2024-08-06)<br><br>Features activated based on block number: activated at block number 247,480,000 (2024-08-13) |
| V2.5 | Emergency bug fix for eSpace CREATE2 opcode security | Features activated based on epoch number: activated at epoch number 118,580,000 (2025-03-18) |
| V3.0 | Align Conflux eSpace Behavior with EVM | (Pending) |

### Dormancy

These CIPs have been dormant, lacking recent discussions or follow-ups.

| CIP Number | Title |
| :-----: | :----- |
| [28](CIPs/cip-28.md) | Include Block Number in Contract Address Calculation |
| [33](CIPs/cip-33.md) | Transaction Fee Pricing Mechanism for Tephys |
| [34](CIPs/cip-34.md) | Contract-Friendly Verification Rules |
| [42](CIPs/cip-42.md) | Support Event-Driven Execution Model |
| [44](CIPs/cip-44.md) | Introduce a VDF Verification Builtin |
| [45](CIPs/cip-45.md) | Introduce Flexible Account Authorization |
| [51](CIPs/cip-51.md) | Support SM-2/3 Verification in Internal Contracts |
| [52](CIPs/cip-52.md) | Custom Eligibility Rules for Sponsorship |
| [102](CIPs/cip-102.md) | Change PoW Mining Algorithm |
| [104](CIPs/cip-104.md) | Create EVM-Compatible Space with Ethereum State |

### Replaced

These CIPs have been replaced by other CIPs.

| CIP Number | Title | Replaced By |
| :-----: | :----- | :-----: |
| [2](CIPs/cip-2.md)  | Prohibit Storage Owner Destruction in Sub-Call | [12](CIPs/cip-12.md) |
| [72](CIPs/cip-72.md) | Support Ethereum-Type Transactions | [90](CIPs/cip-90.md) |
| [80](CIPs/cip-80.md)  | Align Conflux and Ethereum Address Generation Rules | [90](CIPs/cip-90.md)  |

## Database / RPC Breaking CIPs

These CIPs, while not requiring hard fork upgrades, could potentially impact the database or RPC.

| CIP Number | Title | Status |
| :-----: | :----- | :-----: |
| [4](CIPs/cip-4.md) | Add Node Type in Status Message | Activated |
| [11](CIPs/cip-11.md) | Distinguish Status and Heartbeat Messages | Activated |
| [37](CIPs/cip-37.md) | Introduce Base32 Checksum Addresses | Activated |
| [60](CIPs/cip-60.md) | Log VM Errors in Tracer | Activated |

## Application-level CIPs

These CIPs establish standards at the application layer, thereby not involving upgrades to the Conflux nodes.

| CIP Number | Title |
| :-----: | :----- |
| [19](CIPs/cip-19.md) | KeyValue EventLog Standard |
| [23](CIPs/cip-23.md) | Hashing and Signing Typed Structured Data |
| [46](CIPs/cip-46.md) | Introduce Conflux Domain Name Service |
| [109](CIPs/cip-109.md) | RPC API for Wallet Provider |
| [1820](CIPs/cip-1820.md) | Migrate ERC1820 to Conflux |
