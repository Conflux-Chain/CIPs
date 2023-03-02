---
cip: 109
title: RPC API for Custodial Wallet Provider
author: Wenda Zhang<@darwintree>
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/110
status: Draft
type: Backward Compatible
created: 2023-2-23
requires: EIP-1193
---

- [Simple Summary](#simple-summary)
- [Abstract](#abstract)
- [Specification](#specification)
  - [Definitions](#definitions)
  - [RPC API](#rpc-api)
    - [授权接口](#授权接口)
      - [wallet\_authorize](#wallet_authorize)
      - [wallet\_checkAuthorizationState](#wallet_checkauthorizationstate)
      - [wallet\_revoke](#wallet_revoke)
    - [资源请求接口](#资源请求接口)
      - [cfx\_accounts](#cfx_accounts)
      - [cfx\_sendTransaction](#cfx_sendtransaction)
      - [cfx\_chainId](#cfx_chainid)
      - [cfx\_signTypedData\_V4](#cfx_signtypeddata_v4)
      - [personal\_sign](#personal_sign)
    - [其他钱包功能接口](#其他钱包功能接口)
      - [wallet\_switchConfluxChain](#wallet_switchconfluxchain)
      - [wallet\_fullVersion](#wallet_fullversion)
  - [Errors](#errors)
    - [Inherited Errors](#inherited-errors)
    - [Extended Errors](#extended-errors)
- [Rationale](#rationale)
- [Backwards Compatibility](#backwards-compatibility)
- [Test Cases](#test-cases)
- [Implementations](#implementations)
- [Security Considerations](#security-considerations)
- [Copyright](#copyright)
- [Appendix 1: 托管钱包服务实现与安全性建议](#appendix-1-托管钱包服务实现与安全性建议)
  - [实现](#实现)
    - [基于 OAuth2 的实现](#基于-oauth2-的实现)
  - [安全性建议](#安全性建议)
- [Appendix 2: target 参数](#appendix-2-target-参数)
- [Appendix 3: 其他讨论](#appendix-3-其他讨论)
  - [`wallet_authorize`的返回值](#wallet_authorize的返回值)
  - [是否为 cfx\_sendTransaction 添加额外参数](#是否为-cfx_sendtransaction-添加额外参数)
    - [`wallet_getTransactionStatusById`](#wallet_gettransactionstatusbyid)
- [Appendix 4: 可选接口](#appendix-4-可选接口)
  - [net\_version](#net_version)
  - [wallet\_userInfo](#wallet_userinfo)
- [Appendix5: Provider 的初始化](#appendix5-provider-的初始化)

注意 ⚠️：本标准为草案，其内容可能随时变更。

## Simple Summary

本 CIP 提议为托管钱包定义一组 Provider RPC API。

## Abstract

钱包是用户进入区块链的入口。根据实现、密钥存储方式的不同，各类钱包存在很大区别。其中，托管钱包是一类钱包服务：用户的私钥存储在第三方服务器上，用户通过使用托管钱包服务来使用钱包。这类钱包引入了对钱包方的信任，带来了一定风险，但同时也降低了区块链使用的门槛：托管钱包可以向用户屏蔽密钥管理等细节，同时也让用户得以在不安装浏览器插件（或钱包客户端）的场景下与 DAPP 交互。

[EIP-1193](https://eips.ethereum.org/EIPS/eip-1193)对钱包与 DAPP 交互的方式进行了规范。在该 EIP 中，钱包 通过 Provider 对象暴露自身接口，DAPP 则借助 Provider 的接口与钱包进行交互。通过支持新的 RPC API，该 EIP 的功能能够得到扩展。对于托管钱包而言，其实现方式、授权方式、支持的功能与插件钱包存在一定差异，因此，托管钱包服务在兼容EIP-1193时，常常需要定义额外的 RPC API 以提供服务，部分情况下还可能会更改已有的 RPC API。不同的托管钱包提供的 API 可能不一致，这意味着开发者需要为每个不同的托管钱包逐个适配不同的 API，增加了开发的难度和工作量，也限制了托管钱包在开发者中的应用范围。

本文档旨在提出一组适用于托管钱包 RPC API 规范，为开发者提供一致的 RPC API 标准，降低开发者的工作量和开发难度，从而促进托管钱包的更广泛应用。这些 API 规范将涵盖与托管钱包相关的一系列操作，例如授权、取消授权、签名等，此外，本 CIP 还将详细说明每个 API 的参数、返回值和异常情况等内容，以便开发者更好地实现或使用 API。

## Specification

本文中，部分关键词使用黑体标出，用于表示“要求”的程度，这些词语对应 [RFC2119](https://www.rfc-editor.org/rfc/rfc2119) 中的相关规范。

- MUST **必须**。用于标识一定要遵守的规范。
- MUST NOT **禁止**。用于标识一定要遵守的规范。
- SHOULD **应当**，RECOMMENDED **推荐**。用于标识大多数情况下要遵守的规范，但在有特定原因时，可以不遵守。
- SHOULD NOT **不应**，NOT RECOMMENDED **不推荐**。用于标识大多数情况下要遵守的规范，但在有特定原因时，可以不遵守。
- MAY **可以**，OPTIONAL **可选的**。用于标识可选的规范。

### Definitions

- Provider。一个 Javascript 对象，DAPP 通过该对象与钱包交互，如无特殊说明，其遵循[EIP-1193](https://eips.ethereum.org/EIPS/eip-1193)的规范。
- DAPP。去中心化应用程序。
- Remote Procedure Call (RPC)。本文档中一般特指 DAPP 向 Provider 的提交的请求，其处理由 Provider 本身或远端钱包服务器负责。
- 托管钱包服务。托管钱包服务控制用户的私钥，用户通过使用在线的托管钱包服务来使用钱包。也指代托管钱包服务所控制的网页/APP。用户需要与托管钱包服务进行交互才能向 DAPP 授权。

### RPC API

#### 授权接口

##### wallet_authorize

进行账户授权，在该 API 调用后，用户会与托管钱包服务进行交互，对 DAPP 进行授权。该 API 的行为与参数有关。

参数

```ts
{
    target: string,
    availableChains: string[],
    scope: string[]
}
```

1. Object

- target： string

  - 包括以下选项，托管钱包服务**可以**只支持其中部分参数。

    - `blank` 在新标签页中打开授权页面
    - `self` 由当前页面跳转至授权页面
    - `iframe` 在 iframe 中打开授权页面

      > 这三种参数中，blank，self 会将用户引导至托管钱包服务控制的页面进行授权，iframe 则会在页面内的 iframe 进行授权。详细解释参考[Appendix 2: target 参数](#appendix-2-target-参数)。

- availableChains：

  - 限定用户可以选择的区块链网络 ID 列表，如  `['0x405']`(在 Conflux 中 1029 为主网络、1 为测试网)
  - 用户只能在指定的网络列表中进行选择和授权
  - 传空数组表示可选网络是钱包的所有网络

- scope：

  - 决定了授权后能执行的操作种类。托管钱包服务**必须**向用户展示应用申请的 scope 并由用户批准。
    - 可选的，托管钱包服务**可以**允许用户只批准其中一部分。
  - 托管钱包服务**必须**支持 `address` 与 `sign` scope。
    - `address`。 查看地址。
    - `sign`。 申请对数据/交易进行签名的权限。当此权限未被用户批准时，签名接口将不可用。**可选的**，托管钱包服务可以额外支持下面两个子 scope，这将细化 DAPP 申请签名的能力
      - `sign_data`
      - `sign_tx`
  - **可选的**，托管钱包服务可以支持其他 scope，如

    - `phone`
    - `email`
    - `avatar`
    - `openid`
      等

  > `sign` scope 被批准后，并不代表请求签名不再需要用户授权，这取决于托管钱包服务的实现方式。

返回值:

`target`为 self 时上下文被中断，讨论返回值无意义。

- 无返回值。
- 若授权错误则抛出异常。

> 更多关于返回值的讨论见[`wallet_authorize`的返回值](#wallet_authorize的返回值)

示例：

```js
provider.request({
    method: "wallet_authorize"，
    params: [{
        target: "iframe",
        availableChains: ["0x1", "0x405"]
        scope:["address", "sign"]
    }]
}).then(()=>{
    console.log("authorization success")
}).catch(console.error)
```

##### wallet_checkAuthorizationState

确认、更新当前授权状态。

无参数。

返回值： object

```ts
{
    authorized: bool,
    scope: string[]
}
```

- authorized：bool。是否已授权
- scope：string[]。用户已批准的 scope，未授权时为[]

##### wallet_revoke

撤销 DAPP 本地与钱包服务器远端相关 token（如果存在）。无参数。无返回值。撤销失败时抛出错误。

> https://www.rfc-editor.org/rfc/rfc7009

#### 资源请求接口

##### cfx_accounts

获取当前连接的账户在当前连接的网络下的地址

参数：无

返回值：string[]

若已授权，为只包含一个地址的数组，若未授权或者钱包锁定返回`[]`

##### cfx_sendTransaction

签名并发送交易。在`scope`中不包含`sign`（或子权限`sign_tx`）时，**应当** 直接抛出错误。
同时托管钱包服务**应当**以合适的方式辅助用户审查交易（如短信、移动端应用等）。对于`data`无法解析的交易，**可选的**，托管钱包方可以采取警告用户、拒绝签名等方式规避可能的安全风险。

参数：

1. txParams

- from
- to
- data

返回值：字符串

交易哈希。

> 关于此接口的额外讨论见[是否为 cfx_sendTransaction 添加额外参数](#是否为-cfx_sendtransaction-添加额外参数)

##### cfx_chainId

获取当前用户授权网络的 chain id。

参数：无

返回值：string hex

##### cfx_signTypedData_V4

CIP-23 结构化数据签名。在`scope`中不包含`sign`（或子权限`sign_data`）时，**应当** 直接抛出错误。
同时托管钱包服务**应当**以合适的方式辅助用户审查签名内容（如短信、移动端应用等）。

参数：

1. data
   > 参考 [https://github.com/Conflux-Chain/CIPs/blob/2d9fdbdb08f66f705348669a6cd85e2d53509e97/CIPs/cip-23.md](https://github.com/Conflux-Chain/CIPs/blob/2d9fdbdb08f66f705348669a6cd85e2d53509e97/CIPs/cip-23.md)
2. from，地址，签名者

返回值：string

十六进制字符串

##### personal_sign

CIP-23 非结构化消息签名。在`scope`中不包含`sign`（或子权限`sign_tx`）时，**应当** 直接抛出错误。同时托管钱包服务**应当**以合适的方式辅助用户审查签名内容（如短信、移动端应用等）。

参数：

1. msg，字符串，待签名消息。
2. from，地址，签名者

返回值：string

十六进制字符串

#### 其他钱包功能接口

##### wallet_switchConfluxChain

参数：

1. object
   - chainId： hex 字符串

返回值：

无返回值，失败时（不支持的 chain）/ 授权未通过时抛出错误。

> EIP-3326

##### wallet_fullVersion

获取钱包名称与版本

无参数

返回值：对象

- name: 字符串，钱包名称。
- version：字符串，钱包版本, 命名习惯不作要求。

例：

```js
{
  name: "MetaWallet",
  version: "1.0.0"
}
```

```js
{
  name: "anywallet",
  version: "2023.2.23"
}
```

### Errors

error 格式遵循 EIP-1193 的约定。此处额外定义更多错误。

#### Inherited Errors

| status code | name                  |
| ----------- | --------------------- |
| 4001        | User Rejected Request |
| 4100        | Unauthorized          |
| 4200        | Unsupported Method    |
| 4900        | Disconnected          |
| 4901        | Chain Disconnected    |

#### Extended Errors

| status code | name                |                                        |
| ----------- | ------------------- | -------------------------------------- |
| 5000        | SDKNotReady         | SDK 或相关依赖未完成初始化             |
| 6000        | ParamsError         | 调用 RPC API 时参数错误                |
| 6101        | Forbidden           | 没有权限                               |
| 7000        | RequestError        | 请求错误, 具体信息见  Message 和  data |
| 7101        | ServerInternalError | 钱包服务器内部错误                     |
| 7102        | Timeout             | 连接到钱包服务器超时                   |

## Rationale

TBA.

## Backwards Compatibility

TBA.

## Test Cases

TBA.

## Implementations

见[Appendix 1: 托管钱包服务实现与安全性建议](#appendix-1-托管钱包服务实现与安全性建议)。

## Security Considerations

见[Appendix 1: 托管钱包服务实现与安全性建议](#appendix-1-托管钱包服务实现与安全性建议)。

<!-- ## Rationale

本规范中提供的RPC接口设计有如下考虑

### 兼容性

兼容性方面有如下考虑：

- 添加`wallet_authorize`接口

1. 兼容主流RPC标准。如尽量避免破坏`cfx_accounts`的行为。

### 授权流程

一种典型的授权流程是通过`cfx_accounts`(`eth_accounts`)接口同时完成`授权`与`资源请求`两步

本CIP的授权流程主要参考OAuth2授权协议进行设计，分为授权->资源请求两步。

目前存在EIP-1102

### cfx_sendTransaction 的变更

相比标准RPC新增了扩展参数`waitForHash`，

### 使用环境

## 向下兼容

相关说明参考[兼容性](#兼容性)。

## Implementation


## Security Considerations -->

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

## Appendix 1: 托管钱包服务实现与安全性建议

### 实现

本CIP不涉及“实现”与“安全性”的强制性要求，托管钱包服务可以自行选择实现方式。

#### 基于 OAuth2 的实现

`OAuth2`框架为可选的实现方式之一，其优势在于，OAuth2 授权框架有丰富的文档与开源实现，其标准文档对各类潜在的攻击与安全风险进行了讨论，同时也有 BCP（Best Current Practice）文档可作参考。基于 OAuth2 标准进行实现有助于回避协议设计中的潜在安全问题，也有助于提高协议的互操作性。实现时可以参考以下要点：

- 基于[Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636.html)授权流程进行实现，该实现可支持纯前端应用的授权。详细说明参考[OAuth 2.0 for Browser-Based Apps](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps)。
- 注意 access token 的失效时间。如果需要支持 refresh token，建议支持 refresh token 轮换。
- 用户审查交易的方式。一般而言，钱包需要得到用户对特定交易的许可后才会对特定交易进行签名。若基于 OAuth2 进行实现，用户审查交易可能需要额外的依赖，如短信服务、钱包客户端等。

相关文档：

- [The OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749)
- [Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636.html)
- [OAuth 2.0 Security Best Current Practice](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics)
- [OAuth 2.0 for Native Apps](https://www.rfc-editor.org/rfc/rfc8252.html)
- [OAuth 2.0 for Browser-Based Apps](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps)
  - [Token Storage in the Browser](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-token-storage-in-the-browse)
  - [JavaScript Applications obtaining tokens directly](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-javascript-applications-obt)）
- [OpenID Connect Core 1.0 incorporating errata set 1](https://openid.net/specs/openid-connect-core-1_0.html)

### 安全性建议

OAuth2 标准中对各类安全问题进行了讨论，一些问题也适用于其他协议。部分典型问题如：

- [Client Impersonation](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-client-impersonation)
- [Cross-Site Request Forgery Protections](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-cross-site-request-forgery-)
- [Insufficient Redirect URI Validation](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics#name-insufficient-redirect-uri-v)

> 更详细的列表可以参考[实现](#实现)内各推荐文档的 security considerations 部分

此外，以远端加载的方式（如 CDN）使用 wallet SDK 时会引入额外的安全性依赖。如果 CDN 被攻击，相应 SDK 可能被注入恶意代码。如果需要以这种方式使用 SDK，可以使用 integrity 属性或其他方式保证加载脚本的完整性。

```html
<script
  src="https://example.cdn.com/wallet.min.js"
  integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
></script>
```

## Appendix 2: target 参数

[wallet_authorize](#wallet_authorize) 的参数包括

- self
- blank
- iframe

对应用户在哪种窗口中进行授权。self 与 blank 为兼容 OAuth2 流程的参数，将分别在当前窗口/新窗口打开托管钱包服务控制的授权页，授权完成后会携带 token（code）重定向至指定页面（OAuth2 中的`redirect_uri`）参数。

> 目前在 OAuth2 协议中，没有标准文件对基于 iframe 的授权方式进行讨论。可以基于“中间 iframe”的技术进行实现。实现参考：https://developers.google.com/identity/gsi/web/amp/intermediate-iframe?hl=zh-cn

## Appendix 3: 其他讨论

### `wallet_authorize`的返回值

`target`为`blank`时，可以通过`window.opener`与新窗口交互并在原窗口中获得返回值。但需要注意，特定环境中，`window.opener`可能不可用，这有可能会导致`blank`参数的行为与预期不一致。

### 是否为 cfx_sendTransaction 添加额外参数

考虑为`cfx_sendTransaction` 添加额外参数`waitForHash`。在签名前提前返回。

调用接口->用户授权->签名是一个耗时较长的异步过程，直到签名完成前都无法拿到交易哈希。通过添加这一参数可以让 DAPP 更灵活地处理相关逻辑。

waitForHash 决定返回值类型。

参数：

1. txParams

- from
- to
- data

1. waitForHash：（可选）bool，是否等待交易哈希。

返回值：字符串

返回值为 hex 字符串，其意义与 waitForHash 取值有关。

- waitForHash 为空或 true：交易哈希。
- waitForHash 为 false 时：交易标识`walletTxId`。之后通过接口 `wallet_getTransactionStatusById` 获取交易哈希

#### `wallet_getTransactionStatusById`

获取交易授权状态，用于`cfx_sendTransaction`的参数`waitForHash`为 false

参数

1. walletTxId：hex 字符串

返回值

一个对象

```ts
{
    walletTxStatus: string,
    message: string | undefined,
    hash: string ｜ undefined
}
```

- walletTxStatus. 字符串
  - ”approving”, 授权中，用户未进行授权操作或正在进行授权操作
  - ”approved”, 交易已被允许
  - ”rejected”, 交易因各种原因未被批准（用户拒绝、超时等）
  - ”not found”, 未找到对应交易或已超时被清除
  - ”illegal”, 在签名/发送时发现交易不合法
- message: string. 描述交易状态的额外信息
- hash：string. walletTxStatus 为“approved”时包含此字段，为交易哈希

## Appendix 4: 可选接口

本部分标识了部分可选的接口。

### net_version

参数：无
返回值：string network_id

> 对于 DAPP 开发者，更推荐使用`cfx_chainId`

### wallet_userInfo

根据用户批准的 scope 的获取用户信息。

参数: 无

返回值： object

一个对象。取决于已批准的 scope。

<!--
### wallet_sendTransactionWithVerificationCode

一种可选的 -->

## Appendix5: Provider 的初始化

在插件钱包中，Provider 由插件直接注入浏览器，其初始化过程不由 DAPP 控制。而对于托管钱包而言，Provider 的引入取决于 DAPP 是否引入了相应 SDK，其初始化过程由 DAPP 控制。本 CIP 不对 Provider 初始化方式或参数进行约定。下面为几个可行示例，如:

```js
import { Provider } from 'wallet-sdk'; // 使用的钱包SDK

const provider = new Provider({
  clientId: ...,
  ...
})
```

```html
<script
  src="https://example.cdn.com/wallet.min.js"
  integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
></script>
<script type="text/javascript">
  const provider = new window.xxwallet.Provider({
    clientId: ...,
    ...
  })
</script>
```
