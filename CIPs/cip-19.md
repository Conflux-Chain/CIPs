---
cip: 19
title: Key value EventLog Standard
author: Haizhou Wang <Haizhou@conflux-chain.org>
discussions-to: <URL>
status: Draft
type: backward compatible
created: 2020-08-10
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->

A key value event log standard.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

The following standard specifies a key value format event log which include "announcer", "address", "key", "value" fields.
The standard makes it easy for users to use the block chain consensus mechanism to solve the identity and content verification problems of information publishers.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

Although smart contract using block chain solves information distribution, validation and storage at the same time.
But for some DApp, storage are not strict requirements, using `memory` to store data is very expensive and these DApp could storage them self.
This standard is proposed to standardize the format of such information in EventLog.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

```solidity
event Announce(address indexed announcer, bytes indexed keyHash, bytes key, bytes value);
```

- 1.Event MUST be `Announce(address,bytes,bytes,bytes)` , which signature MUST be `0x14cb751d0950ff2788201931c45f715f7472443bc197311d9e3a7a0ba566b7e6`
- 2.Event MUST NOT be `anonymous`
- 3.Event `announcer` and `keyHash` MUST be `indexed`, `key` and `value` MUST NOT be `indexed`
- 4.Event `keyHash` MUST be `keccak256` hash of `key`

Note: The standard only specifies the format, but not the specific implementation method.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

* Data structure

`Announce` event log:

```json
{
  "epochNumber": 1652588,
  "logIndex": 0,
  "transactionIndex": 0,
  "transactionLogIndex": 0,
  "address": "0x8bf8f3d98519dc933a0105356d080237a2e939a7",
  "blockHash": "0xda45520d27897c164306e8f2e20e18e1f83d74f5015fbea663069767da65af71",
  "data": "0x0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000008000000000000000000000000000000000000000000000000000000000000000036b65790000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000576616c7565000000000000000000000000000000000000000000000000000000",
  "topics": [
    "0x14cb751d0950ff2788201931c45f715f7472443bc197311d9e3a7a0ba566b7e6",
    "0x0000000000000000000000001bd9e9be525ab967e633bcdaeac8bd5723ed4d6b",
    "0x07855b46a623a8ecabac76ed697aa4e13631e3b6718c8a0d342860c13c30d2fc"
  ],
  "transactionHash": "0xca05ebc9c8eb6c1f03ae82f008335f4e76c9f2a275f87c08defd448167790c8a"
}
``` 

explanation:

```
address: smart contract address
topics[0]: event log signature
topics[1]: address of announcer
topics[2]: keccak256 hash of key field
data: encode of key and value data, decoded as {key:0x6b6579, value:0x76616c7565}, which is {key:"key", value:"value"} in utf8 code.
```

* Gas cost

EventLog only stays in block chain node for a period of time, and the timeout information will not be found, nor will it occupy the node's storage resources indefinitely.
This standard does not need to occupy `storageCollateralized` and the transaction costs can be minimized to include only the `gas` portion.

* Application scenarios

Information distribution and validation for DApp.
Real time chat application.
News or stream media.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
TBD

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

* mini implement of Announce:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.10;

contract Announcement {
    event Announce(address indexed announcer, bytes indexed keyHash, bytes key, bytes value);

    function announce(bytes calldata key, bytes calldata value) external {
        emit Announce(msg.sender, key, key, value);
    }
}
```

call `announce()` with `key`, `value` and contract set `msg.sender` to `announcer`

* The standard allows multiple `Announce` event log from one transaction, contract could generate multiple `Announce` to reduce transaction as follows.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
pragma abicoder v2;

contract Announcement {
    struct Entry {
        bytes key;
        bytes value;
    }

    event Announce(address indexed announcer, bytes indexed keyHash, bytes key, bytes value);

    function announce(Entry[] calldata array) external {
        for (uint i = 0; i < array.length; i++) {
            emit Announce(msg.sender, array[i].key, array[i].key, array[i].value);
        }
    }
}
```

* Standard can be embedded in other contracts, also can be extended.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

* Conflux Transaction data length is limited, large key and value may cause the transaction fail.
* Standard not require `announcer` must be `msg.sender`.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
