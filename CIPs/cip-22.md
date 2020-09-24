---
cip: 22
title:  Add interfaces for Internal contracts 
author: Yu Xiong <@oo7ww>, Chenxing Li<chenxing@conflux-chain.org>
discussions-to: https://github.com/Conflux-Chain/conflux-rust/issues/1784
status: Draft
type: Spec Breaking
created: 2020-08-17
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
In this CIP we propose to add **query** and a few **help** interfaces for internal contracts. 

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Currently the internal contracts have no **query** interfaces, and especially SponsorWhiteControl contract lacks **help** functions to quickly maintain whitelist for contracts moved from Ethereum-like chains.  These interfaces are needed for further development and make it easy for developers to build contracts on Conflux chain.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Internal contracts provide **set** methods, but there is no **get** methods to get the data in internal contracts. The invisible data in internal contracts makes it difficult to develop other contracts. The existing SponsorWhiteControl contract ignores developers who try to move their work from Ethereum-like chain to Conflux chain. For the reason explained above, Conflux should provide these interfaces for developers. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

Specific functions in solidity is detailed as below :

### 1 Sponsorship for Usage of Contracts

* addPrivilege/removePrivilege functions limited to admin: this will help developers to modify whitelist after moving their contracts from other chains.

```solidity
pragma solidity >=0.4.15;

contract SponsorWhitelistControl {
    /*** Query Functions ***/
    /**
     * @dev get gas sponsor address of specific contract
     * @param contract_addr The address of the sponsored contract
     */
    function getSponsorforGas(address contract_addr) public returns (address) {}

    /**
     * @dev get current Sponsored Balance for gas
     * @param contract_addr The address of the sponsored contract
     */
    function getSponsoredBalanceforGas(address contract_addr) public returns (uint) {}

    /**
     * @dev get current Sponsored Gas fee upper bound
     * @param contract_addr The address of the sponsored contract
     */
    function getSponsoredGasFeeUpperbound(address contract_addr) public returns (uint) {}

    /**
     * @dev get collateral sponsor address
     * @param contract_addr The address of the sponsored contract
     */
    function getSponsorforCollateral(address contract_addr) public returns (address) {}

    /**
     * @dev get current Sponsored Balance for collateral
     * @param contract_addr The address of the sponsored contract
     */
    function getSponsoredBalanceforCollateral(address contract_addr) public returns (uint) {}

    /**
     * @dev check if a user is in a contract's whitelist
     * @param contract_addr The address of the sponsored contract
     * @param user The address of contract user
     */
    function isWhitelisted(address contract_addr, address user) public returns (bool) {}

    /**
     * @dev check if all users are in a contract's whitelist
     * @param contract_addr The address of the sponsored contract
     */
    function isAllWhitelisted(address contract_addr) public returns (bool) {}

    /*** for contract admin only **/
    /**
     * @dev contract admin add user to whitelist
     * @param contract_addr The address of the sponsored contract
     * @param addresses The user address array
     */
    function addPrivilegebyAdmin(address contract_addr, address[] memory addresses) public {}

    /**
     * @dev contract admin remove user from whitelist
     * @param contract_addr The address of the sponsored contract
     * @param addresses The user address array
     */
    function removePrivilegeByAdmin(address contract_addr, address[] memory addresses) public {}

    // ------------------------------------------------------------------------
    // Someone will sponsor the gas cost for contract `contract_addr` with an
    // `upper_bound` for a single transaction.
    // ------------------------------------------------------------------------
    function setSponsorforGas(address contract_addr, uint upper_bound) public payable {}

    // ------------------------------------------------------------------------
    // Someone will sponsor the storage collateral for contract `contract_addr`.
    // ------------------------------------------------------------------------
    function setSponsorForCollateral(address contract_addr) public payable {}

    // ------------------------------------------------------------------------
    // Add commission privilege for address `user` to some contract.
    // ------------------------------------------------------------------------
    function addPrivilege(address[] memory) public {}

    // ------------------------------------------------------------------------
    // Remove commission privilege for address `user` from some contract.
    // ------------------------------------------------------------------------
    function removePrivilege(address[] memory) public {}
}
```
### 2 Admin Management
```solidity
pragma solidity >=0.4.15;

contract AdminControl {
    /*** Query Functions ***/
    /**
     * @dev get admin of specific contract
     * @param contract_addr The address of specific contract
     */
    function getAdmin(address contract_addr) public returns (address) {}

    function setAdmin(address, address) public {}
    function destroy(address) public {}
}
```
### 3 Staking Mechanism
```solidity
pragma solidity >=0.4.15;

contract Staking {
    /*** Query Functions ***/
    /**
     * @dev get user's staking balance
     * @param user The address of specific user
     */
    function getStakingBalance(address user) public returns (uint) {}

    /**
     * @dev get user's locked staking balance at given blockNumber
     * @param user The address of specific user
     * @param blockNumber The blockNumber as index.
     */
    // ------------------------------------------------------------------------
    // Note: if the blockNumber is less than the current block number, function
    // will return current locked staking balance.
    // ------------------------------------------------------------------------
    function getLockedStakingBalance(address user, uint blockNumber) public returns (uint) {}


    /**
     * @dev get user's vote power staking balance at given blockNumber
     * @param user The address of specific user
     * @param blockNumber The blockNumber as index.
     */
    // ------------------------------------------------------------------------
    // Note: if the blockNumber is less than the current block number, function
    // will return current vote power.
    // ------------------------------------------------------------------------
    function getVotePower(address user, uint blockNumber) public returns (uint) {}

    function deposit(uint amount) external {}

    function withdraw(uint amount) external {}

    function voteLock(uint amount, uint unlock_block_number) external {}
}
```

### Gas consumption for internal contracts

For all the query interfaces (the name started with `get`), the gas consumption is `400 + ceil(return_length/32) * 3`, except the following two interfaces. 
- `getLockedStakingBalance`: `200 * (vote_stake_list_len + 1) + ceil(return_length / 32) * 3`
- `getVotePower`: `200 * (vote_stake_list_len + 1) + ceil(return_length / 32) * 3`

Here the `return_length` refers the number of bytes of abi encoded return value.

The gas requirement for the other new interfaces are list as follows.
- `isWhitelisted`:  `200 + ceil(return_length/32) * 3`
- `isAllWhitelisted`: `200 + ceil(return_length/32) * 3`
- `addPrivilegebyAdmin`: same as `addPrivilege`
- `removePrivilegebyAdmin`: same as `removePrivilege`


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

There is no more details to be explained.


## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This proposal is not backwards compatible. Because this introduce new functions to internal contracts and make contract execution have different behaviors.

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->

TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

This proposal brings no security issues. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
