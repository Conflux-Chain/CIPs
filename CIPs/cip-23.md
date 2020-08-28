---
cip: 23
title: Typed structured data hashing and signing
author: Chenxing Li <chenxing@conflux-chain.org>
discussions-to: <URL>
status: Draft
type: Backward Compatible
created: 2020-08-28
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->
Signing data is a solved problem if all we care about are bytestrings. Unfortunately in the real world we care about complex meaningful messages. Hashing structured data is non-trivial and errors result in loss of the security properties of the system. Conflux should provide a method for signing typed data like Ethereum EIP-712.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
This is a standard for hashing and signing of typed structured data as opposed to just bytestrings. It includes a

*   theoretical framework for correctness of encoding functions,
*   specification of structured data similar to and compatible with Solidity structs,
*   safe hashing algorithm for instances of those structures,
*   safe inclusion of those instances in the set of signable messages,
*   an extensible mechanism for domain separation, and
*   an optimized implementation of the hashing algorithm in EVM.

It does not include replay protection.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->
This CIP aims to provide a method for off-chain message signing which is compatible with Ethereum's community. 


## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Summary for difference with EIP-712

The specification is derived from [EIP-712](https://github.com/ethereum/EIPs/blob/3194278525e4ffec192dd75d3e18bf648e28524e/EIPS/eip-712.md). Conflux has the following differences with EIP-712. 

- When encoding a bytestring `message ∈ 𝔹⁸ⁿ`, Conflux add a prefix `\x19Conflux Signed Message:\n` instead of `\x19Ethereum Signed Message:\n`.
- Rename `EIP712domain` to `CIP23domain`
- In `CIP23domain`, the `chainId` field must be included.

### Encoding method

The set of signable messages is extended from transactions and bytestrings `𝕋 ∪ 𝔹⁸ⁿ` to also include structured data `𝕊`. The new set of signable messages is thus `𝕋 ∪ 𝔹⁸ⁿ ∪ 𝕊`. They are encoded to bytestrings suitable for hashing and signing as follows:

*   `encode(transaction : 𝕋) = RLP_encode(transaction)`
*   `encode(message : 𝔹⁸ⁿ) = "\x19Conflux Signed Message:\n" ‖ len(message) ‖ message` where `len(message)` is the _non-zero-padded_ ascii-decimal encoding of the number of bytes in `message`.
*   `encode(domainSeparator : 𝔹²⁵⁶, message : 𝕊) = "\x19\x01" ‖ domainSeparator ‖ hashStruct(message)` where `domainSeparator` and `hashStruct(message)` are defined below.

The 'version byte' is fixed to `0x01`, the 'version specific data' is the 32-byte domain separator `domainSeparator` and the 'data to sign' is the 32-byte `hashStruct(message)`.

#### Definition of typed structured data `𝕊`

To define the set of all structured data, we start with defining acceptable types. Like ABIv2 these are closely related to Solidity types. It is illustrative to adopt Solidity notation to explain the definitions. The standard is specific to the Ethereum Virtual Machine, but aims to be agnostic to higher level languages. Example:

```Solidity
struct Mail {
    address from;
    address to;
    string contents;
}
```

**Definition**: A _struct type_ has valid identifier as name and contains zero or more member variables. Member variables have a member type and a name.

**Definition**: A _member type_ can be either an atomic type, a dynamic type or a reference type.

**Definition**: The _atomic types_ are `bytes1` to `bytes32`, `uint8` to `uint256`, `int8` to `int256`, `bool` and `address`. These correspond to their definition in Solidity. Note that there are no aliases `uint` and `int`. Note that contract addresses are always plain `address`. Fixed point numbers are not supported by the standard. Future versions of this standard may add new atomic types.

**Definition**: The _dynamic types_ are `bytes` and `string`. These are like the atomic types for the purposed of type declaration, but their treatment in encoding is different.

**Definition**: The _reference types_ are arrays and structs. Arrays are either fixed size or dynamic and denoted by `Type[n]` or `Type[]` respectively. Structs are references to other structs by their name. The standard supports recursive struct types.

**Definition**: The set of structured typed data `𝕊` contains all the instances of all the struct types.

### Definition of `hashStruct`

The `hashStruct` function is defined as

*   `hashStruct(s : 𝕊) = keccak256(typeHash ‖ encodeData(s))` where `typeHash = keccak256(encodeType(typeOf(s)))`

**Note**: The `typeHash` is a constant for a given struct type and does not need to be runtime computed.

#### Definition of `encodeType`

The type of a struct is encoded as `name ‖ "(" ‖ member₁ ‖ "," ‖ member₂ ‖ "," ‖ … ‖ memberₙ ")"` where each member is written as `type ‖ " " ‖ name`. For example, the above `Mail` struct is encoded as `Mail(address from,address to,string contents)`.

If the struct type references other struct types (and these in turn reference even more struct types), then the set of referenced struct types is collected, sorted by name and appended to the encoding. An example encoding is `Transaction(Person from,Person to,Asset tx)Asset(address token,uint256 amount)Person(address wallet,string name)`.

#### Definition of `encodeData`

The encoding of a struct instance is `enc(value₁) ‖ enc(value₂) ‖ … ‖ enc(valueₙ)`, i.e. the concatenation of the encoded member values in the order that they appear in the type. Each encoded member value is exactly 32-byte long.

The atomic values are encoded as follows: Boolean `false` and `true` are encoded as `uint256` values `0` and `1` respectively. Addresses are encoded as `uint160`. Integer values are sign-extended to 256-bit and encoded in big endian order. `bytes1` to `bytes31` are arrays with a beginning (index `0`) and an end (index `length - 1`), they are zero-padded at the end to `bytes32` and encoded in beginning to end order. This corresponds to their encoding in ABI v1 and v2.

The dynamic values `bytes` and `string` are encoded as a `keccak256` hash of their contents.

The array values are encoded as the `keccak256` hash of the concatenated `encodeData` of their contents (i.e. the encoding of `SomeType[5]` is identical to that of a struct containing five members of type `SomeType`).

The struct values are encoded recursively as `hashStruct(value)`. This is undefined for cyclical data.

#### Definition of `domainSeparator`


```Solidity
domainSeparator = hashStruct(cip23Domain)
```

where the type of `cip23Domain` is a struct named `CIP23Domain` with one or more of the below fields (the `chainId` field must be included.) Protocol designers only need to include the fields that make sense for their signing domain. Unused fields are left out of the struct type.

*   `string name` the user readable name of signing domain, i.e. the name of the DApp or the protocol.
*   `string version` the current major version of the signing domain. Signatures from different versions are not compatible.
*   `uint256 chainId` the chain id. The user-agent *should* refuse signing if it does not match the currently active chain.
*   `address verifyingContract` the address of the contract that will verify the signature. The user-agent *may* do contract specific phishing prevention.
*   `bytes32 salt` an disambiguating salt for the protocol. This can be used as a domain separator of last resort.

Future extensions to this standard can add new fields with new user-agent behaviour constraints. User-agents are free to use the provided information to inform/warn users or refuse signing.


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
We refer the reader to [EIP-712](https://github.com/ethereum/EIPs/blob/3194278525e4ffec192dd75d3e18bf648e28524e/EIPS/eip-712.md) for rationale discussion.


## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This CIP only provides a standard for signing messages to be read by the smart contract. It is backwards compatible. 


## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBA.

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
TBA.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

The main security problem is cross-chain replay attack. If the user signs a message in Conflux which is also valid on Ethereum, an attacker may replay the message and signature on Ethereum. Since the transaction and domain separator in typed struct is bound with chain id, the cross-chain replay attack can be prevented if Conflux has a distinct chain id.



## History

This document was derived from [Ethereum's EIP-712](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-712.md) written by Remco Bloemen, Leonid Logvinov and Jacob Evans. Their authors are not responsible for its use in the Conflux Improvement Proposal, and should not be bothered with technical questions specific to Conflux or the CIP. Please direct all comments to the CIP authors.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
