---
cip: 31
title: New internal contract: Create2Factory
author: Fan Wang(@posaggen)
discussions-to: <URL>
status: Draft
type: Spec Breaking
created: 2020-10-10
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Add a new internal contract Create2Factory in the genesis block, which is used to deploy smart contracts with create2 op code.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Create a new internal contract Create2Factory in genesis block with a fixed private key and fixed nonce.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

There are some common smart contracts such as ERC1820 that used in different Defi scenarios, it will be convenient if the address of a common smart contract can be the same among mainnet, testnet and private network. Create2Factory contract can make this easy by choosing same create2 salt parameter for a common smart contract on different network. Furthermore, the address of contract deployed by create2 is computed from salt parameter, so it is possible to make the address of common smart contract more distinguishable by picking a proper salt.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Create2Factory.sol
```solidity
pragma solidity 0.5.11;

contract Create2Factory {
    function deploy(
        bytes memory code,
        uint256 salt
    ) public returns (address) {
        address addr;
        assembly {
            addr := create2(0, add(code, 0x20), mload(code), salt)
            if iszero(extcodesize(addr)) {
                revert(0, 0)
            }
        }
        return addr;
    }
}
```
### Create2Factory bytecode
```solidity
0x608060405234801561001057600080fd5b50610157806100206000396000f3fe608060405234801561001057600080fd5b506004361061002b5760003560e01c80639c4ae2d014610030575b600080fd5b6100d86004803603604081101561004657600080fd5b81019060208101813564010000000081111561006157600080fd5b82018360208201111561007357600080fd5b8035906020019184600183028401116401000000008311171561009557600080fd5b91908080601f0160208091040260200160405190810160405280939291908181526020018383808284376000920191909152509295505091359250610101915050565b6040805173ffffffffffffffffffffffffffffffffffffffff9092168252519081900360200190f35b600080828451602086016000f59050803b61011b57600080fd5b939250505056fea265627a7a7231582057d975021b6d8a5e27e8f99cc80dd85ee795b4662dd1be9131dc75b0a102c91664736f6c634300050b0032
```

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

None.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This CIP is backward compatible. 

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD


## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
Put a fixed transaction in genesis block to create a new internal contract Create2Factory.


## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
None.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).