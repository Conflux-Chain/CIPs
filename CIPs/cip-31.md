---
CIP No.: 31
Title: "Genesis Block Contract: Create2Factory"
Author: Fan Wang(@posaggen)
Status: Final
Type: Spec Breaking
Created: 2020-10-10
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Add a new contract Create2Factory in the genesis block, which is used to deploy smart contracts with create2 op code.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Put a fixed transaction in genesis block to create contract Create2Factory.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

There are some common smart contracts such as ERC1820 that used in different Defi scenarios, it will be convenient if the address of a common smart contract can be the same among mainnet, testnet and private network. Create2Factory contract can make this easy by choosing same create2 salt parameter for a common smart contract on different network. Furthermore, the address of contract deployed by create2 is computed from salt parameter, so it is possible to make the address of common smart contract more distinguishable by picking a proper salt.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Create2Factory.sol
```solidity
pragma solidity 0.5.11;

contract Create2Factory {
    mapping(address => bool) deployed;

    function isDeployed(address addr) public view returns(bool) {
      return deployed[addr]; 
    }

    function deploy(
        bytes memory code,
        uint256 salt
    ) public returns (address) {
        address addr;
        bool success = true;
        assembly {
            addr := create2(0, add(code, 0x20), mload(code), salt)
            if iszero(extcodesize(addr)) {
              success := 0
            }
        }
        require(success, "create2 failed");
        require(!deployed[addr], "contract has been deployed before");
        deployed[addr] = true;
        return addr;
    }
}

```
### Create2Factory bytecode
```solidity
0x608060405234801561001057600080fd5b506102a2806100206000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c806390184b021461003b5780639c4ae2d014610075575b600080fd5b6100616004803603602081101561005157600080fd5b50356001600160a01b0316610139565b604080519115158252519081900360200190f35b61011d6004803603604081101561008b57600080fd5b8101906020810181356401000000008111156100a657600080fd5b8201836020820111156100b857600080fd5b803590602001918460018302840111640100000000831117156100da57600080fd5b91908080601f0160208091040260200160405190810160405280939291908181526020018383808284376000920191909152509295505091359250610157915050565b604080516001600160a01b039092168252519081900360200190f35b6001600160a01b031660009081526020819052604090205460ff1690565b600080600060019050838551602087016000f59150813b610176575060005b806101c8576040805162461bcd60e51b815260206004820152600e60248201527f63726561746532206661696c6564000000000000000000000000000000000000604482015290519081900360640190fd5b6001600160a01b03821660009081526020819052604090205460ff16156102205760405162461bcd60e51b815260040180806020018281038252602181526020018061024d6021913960400191505060405180910390fd5b506001600160a01b0381166000908152602081905260409020805460ff1916600117905590509291505056fe636f6e747261637420686173206265656e206465706c6f796564206265666f7265a265627a7a723158200af37f6335cc41d7dfae2771f8663b4a48fc54c11ec8a28682901a0951f9ce5364736f6c634300050b0032
```

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

None.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->

This CIP is not backward compatible. 

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD


## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
Put a fixed transaction in genesis block to create contract Create2Factory. The implementation is https://github.com/Conflux-Chain/conflux-rust/pull/1890, address of Create2factory is 0x8a3a92281df6497105513b18543fd3b60c778e40.


## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
None.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).