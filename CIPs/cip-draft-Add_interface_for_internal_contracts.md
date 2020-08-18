---
cip: <to be assigned>
title:  Add Query interfaces for Internal contracts 
author: Yu Xiong <@oo7ww> 
discussions-to: https://github.com/Conflux-Chain/conflux-rust/issues/1784
status: Draft
type: Protocol Breaking
created: 2020-08-17
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Add **Query** interfaces for internal contracts. 

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Internal contracts have no **Query** interfaces.  These interfaces are needed for further development.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
Internal contracts provide **set** methods, but there is no **query** methods to get the data in internal contracts. The invisible data in internal contracts makes it difficult to develop other contracts. So, **query** methods should be added. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

Specific functions in solidity is detailed as below :

### 1 Sponsorship for Usage of Contracts

```solidity
pragma solidity >=0.4.15;

contract SponsorWhitelistControl {
    /*** Query Funtions ***/
  	// get gas sponsor address of specific contract
  	function getSponsorForGas(address contract) public returns (address) {
    }
  	// get current Sponsored Balance for gas
		function getSponsoredBalanceForGas(address contract) public returns (uint) {
    }
  	// get current Sponsored Gas fee upper bound
		function getSponsoredGasFeeUpperBound(address contract) public returns (uint) { 
    }
		//get collateral sponsor address 
  	function getSponsorForCollateral(address contract) public returns (address) {
    }
		//get curretn Sponsored Balance for collateral
    function getSponsoredBalanceForCollateral(address contract) public returns (uint) {
    }
    //check whitelist
    function isWhitelisted(address contract, address user) public returns (bool) {
    }
		function isAllWhitelisted(address contract) public returns (bool) {
    }

    /*** for contract admin only **/
    function addPrivilege(address contract, address[] memory) public {
    }
		function removePrivilege(address contract, address[] memory) public {
    }

  	// ------------------------------------------------------------------------
    // Someone will sponsor the gas cost for contract `contract_addr` with an
    // `upper_bound` for a single transaction.
    // ------------------------------------------------------------------------
    function set_sponsor_for_gas(address contract_addr, uint upper_bound) public payable {
    }

    // ------------------------------------------------------------------------
    // Someone will sponsor the storage collateral for contract `contract_addr`.
    // ------------------------------------------------------------------------
    function set_sponsor_for_collateral(address contract_addr) public payable {
    }

    // ------------------------------------------------------------------------
    // Add commission privilege for address `user` to some contract.
    // ------------------------------------------------------------------------
    function add_privilege(address[] memory) public {
    }

    // ------------------------------------------------------------------------
    // Remove commission privilege for address `user` from some contract.
    // ------------------------------------------------------------------------
    function remove_privilege(address[] memory) public {
    }
}
```
### 2 Admin Management
```solidity
pragma solidity >=0.4.15;

contract AdminControl {
  	/*** Query Functions ***/
  	//get admin of specific contract
  	function getAdmin(address contract) public returns (address) {
    }

    function set_admin(address, address) public {}
    function destroy(address) public {}
}
```
### 3 Staking Mechanism
```solidity
pragma solidity >=0.4.15;

contract Staking {
    /*** Query Functions ***/
  	function getStakingBalance(address user) public returns (uint) {
    }
		function getVoteLocked(address user) public returns (uint) {
    }
		function getVoteUnlockBlockNumber(address user) public returns (uint) {
    }
  
  	function deposit(uint amount) external {
    }

    function withdraw(uint amount) external {
    }

    function vote_lock(uint amount, uint unlock_block_number) external {
    }
}
```

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

There is no more details to be explained.


## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This proposal is backwards compatible.

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
