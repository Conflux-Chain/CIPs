---
cip: <to be assigned>
title: Key value EventLog Standard
author: Haizhou Wang <Haizhou@conflux-chain.org>
discussions-to: <URL>
status: Draft
type: Protocol Breaking
created: 2020-08-10
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the CIP.-->

一种 KV EventLog 的格式标准

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

该标准规定一种 KV 格式的 EventLog. 通过该格式可以描述谁(announcer) 在什么地方(address) 就某一主题(key) 发表什么内容(value).
该标准让使用者方便的利用区块链的共识机制, 解决信息发布者身份和内容验证的问题.

## Motivation
<!--The motivation is critical for CIPs that want to change the Conflux protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the CIP solves. CIP submissions without sufficient motivation may be rejected outright.-->

虽然智能合约提供了`内存`机制, 利用区块链同时解决了信息的**分发**,**验证**和**存储**三个功能, 使得数据"上链", 
但部分应用对**存储**的功能要求不严, 使用`内存`存储数据价格昂贵且功能过剩.
此类应用可以通过智能合约的`Event`机制, 利用区块链解决信息的**分发**,**验证**问题, 应用对信息不作存储或自己解决**存储**功能, 即数据"过链".
为此提出该标准以规范这类信息在 EventLog 中的格式.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

```solidity
event Announce(address indexed announcer, bytes indexed keyHash, bytes key, bytes value);
```

- 1.Event 格式必须为 `Announce(address,bytes,bytes,bytes)` 即签名必须为 `0x14cb751d0950ff2788201931c45f715f7472443bc197311d9e3a7a0ba566b7e6`
- 2.Event 必须为非匿名
- 3.Event 的 announcer 和 keyHash 字段必须为索引项 `indexed`, key 和 value 字段必须为非索引项.
- 4.Event 的 keyHash 必须为 key 字段的 `keccak256` 哈希值

备注: 该标准只规定了 Event 的格式, 对其具体实现方式不作要求.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

* 设计思路

标准将信息分为 主题(key) 和 内容(value) 两个部分, 
对 key 和 value 类型规定均为 bytes 以兼容二进制数据的应用场景, 
为了便于通过 RPC 搜索相应主题(key) 标准要求对主题(key) 进行索引(keyHash)

* 数据结构

该标准产生 EventLog 形如:

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

解析其中信息可知:
```
address 为产生 event 的合约地址
topics[0] 表示这是一个 Announce 的 EventLog
topics[1] 为发布 Announce 的用户(announcer)
topics[2] 为 Announce 主题(key) 的哈希值
data 包含 主题(key) 和 内容(value) 信息, 解析后为 key:0x6b6579, value:0x76616c7565 即 utf8 编码的 key:"key", value:"value"
```

* 时效与费用

EventLog 只在节点中保留一段时间, 超时的信息将无法在 FullNode 中查到, 同时也不会无限时占用节点存储资源.
标准不需占用 storageCollateralized, 交易费用最低可仅包含 gas 部分.

* 应用场景

解决信息认证问题, 用户可以基于此开发如及时聊天软件, 新闻甚至流媒体.

## Backwards Compatibility
<!--All CIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The CIP must explain how the author proposes to deal with these incompatibilities. CIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
TBD

## Test Cases
<!--Test cases for an implementation are mandatory for CIPs that are affecting consensus changes. Other CIPs can choose to include links to test cases if applicable.-->
TBD

## Implementation
<!--The implementations must be completed before any CIP is given status "Final", but it need not be completed before the CIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

* 以下为 Announce 的一种最简实现方式:

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

通过调用 `announce()` 方法传递 key, value. 合约会将交易的发送者设为 announcer.

* 标准允许一条交易产生多个 Announce EventLog, 可以对合约扩展如下以同时产生多个 EventLog 以减少交易发送次数.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.10;
pragma experimental ABIEncoderV2;

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

* 标准可以嵌入其他合约中使用, 也可对标准进行扩展, 如约束产生 Announce 条件和内容.

## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

* Conflux 对 Block 的大小有所限制, 过大的 key 或 value 可能会导致交易执行失败
* 标准要求 keyHash 字段必须为 key 的 `keccak256` 哈希值, 因此存在哈希冲突的可能
* 标准对实现方式不作要求, 各个字段值准确含义需参考具体实现 (如 announcer 不一定是交易发送者) 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
