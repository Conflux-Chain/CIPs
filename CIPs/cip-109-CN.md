---
cip: 109
title: Clientless Host Wallet Provider API
author: <a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s), e.g. (use with the parentheses or triangular brackets): FirstName LastName (@GitHubUsername), FirstName LastName <foo@bar.com>, FirstName (@GitHubUsername) and GitHubUsername (@GitHubUsername)>
discussions-to: <URL>
status: Draft
# type: Backward Compatible
created: 2023-2-23
requires (*optional): EIP-1193
replaces (*optional): <CIP number(s)>
resolution (*optional): <URL>
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
  - [Errors](#errors)
    - [Inherited Errors](#inherited-errors)
    - [Extended Errors](#extended-errors)
- [Copyright](#copyright)
- [Appendix 1: 托管钱包提供商实现与安全性建议](#appendix-1-托管钱包提供商实现与安全性建议)
  - [实现](#实现)
  - [安全性建议](#安全性建议)
- [Appendix 2: target 参数](#appendix-2-target-参数)
- [Appendix 3: 其他讨论](#appendix-3-其他讨论)
  - [是否为 cfx\_sendTransaction 添加额外参数](#是否为-cfx_sendtransaction-添加额外参数)
- [Appendix 4: 可选接口](#appendix-4-可选接口)
      - [net\_version](#net_version)
      - [wallet\_userInfo](#wallet_userinfo)

注意⚠️：本标准为草案，其内容可能随时变更。

## Simple Summary

本 CIP 提议为基于浏览器的无客户端托管钱包定义一组Provider RPC API。

## Abstract

一般情况下，用户与DAPP的交互需要使用钱包客户端。钱包客户端保存用户的私钥，并可用于与DAPP进行各种业务逻辑的交互。钱包客户端可包括浏览器插件、桌面应用程序、移动应用程序或硬件设备等。由于用户的私钥存储在钱包客户端中，DAPP无法接触到用户的私钥，因此用户的安全得到了保障。

相反，托管钱包是由第三方机构或服务提供商托管的钱包，用户的私钥存储在第三方服务器上而非用户自己的设备上。这种钱包引入了对钱包方的信任，但同时也带来了好处：用户无需手动管理私钥，并能够在未安装钱包客户端的情况下与DAPP进行交互。

[EIP-1193](https://eips.ethereum.org/EIPS/eip-1193)对钱包与DAPP交互的方式进行了规范。在该EIP中，钱包会向页面中注入“Provider”对象，并通过Provider对象暴露自身接口，DAPP则通过Provider暴露的接口与钱包进行交互。通过支持新的RPC协议，遵循该规范的实现能够不断扩充功能。对于无客户端的托管钱包（为简便，下文中，“托管钱包”均指无客户端的托管钱包），其授权模式、支持的功能与插件钱包存在一定差异，因此，托管钱包提供商常常需要定义额外的RPC API以提供服务，部分情况下还可能会更改现有的RPC协议。不同的托管钱包提供的API可能不一致，这意味着开发者需要为每个不同的托管钱包逐个适配不同的API，增加了开发的难度和工作量，也限制了托管钱包在开发者中的应用范围。

本文档旨在提出一组适用于托管钱包基于RPC API的规范，为开发者提供一致的API标准，降低开发者的工作量和开发难度，从而促进托管钱包的更广泛应用。这些API规范将涵盖与托管钱包相关的一系列操作，例如授权、取消授权、签名等，并旨在实现不同托管钱包之间的互操作性。此外，这些规范还将详细说明每个API的参数、返回值和异常情况等内容，帮助开发者更好地使用API。

> 本CIP不规定具体实现方式，但尽量保证了对OAuth2的兼容。

## Specification

<!-- 本文中，部分关键词使用黑体标出，用于表示“要求”的程度，这些词语对应 [RFC2119](https://www.rfc-editor.org/rfc/rfc2119) 中的相关规范。

- MUST **必须**。用于标识一定要遵守的规范。
- MUST NOT **禁止**。用于标识一定要遵守的规范。
- SHOULD **应当**，RECOMMENDED **推荐**。用于标识大多数情况下要遵守的规范，但在有特定原因时，可以不遵守。
- SHOULD NOT **不应**，NOT RECOMMENDED **不推荐**。用于标识大多数情况下要遵守的规范，但在有特定原因时，可以不遵守。
- MAY **可以**，OPTIONAL **可选的**。用于标识可选的规范。 -->

### Definitions

- Provider。一个Javascript对象，DApp通过该对象与钱包交互，如无特殊说明，其遵循[EIP-1193](https://eips.ethereum.org/EIPS/eip-1193)的规范。
- DAPP。去中心化应用程序。
- Remote Procedure Call (RPC)。本文档中指DAPP向Provider的提交的请求，其处理由Provider本身或远端钱包服务器负责。
- 托管钱包提供商。托管钱包服务的提供者，部分语境下也指代托管钱包提供商所控制的网页。

### RPC API

#### 授权接口

##### wallet_authorize

进行账户授权，在该 API 调用后，用户需要与托管钱包提供商进行交互，对DAPP进行授权。该 API 的行为与参数有关。

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
    - 包括以下选项，托管钱包提供商可以只支持其中部分参数。
      - `blank` 在新标签页中打开授权页面
      - `self` 由当前页面跳转至授权页面
      - `iframe` 在 iframe 中打开授权页面

        > 这三种参数中，blank，self 会将用户引导至托管钱包提供商控制的页面进行授权，完成授权后用户将被重定向至指定 uri。iframe 流程建议通过”中间 iframe“方式实现。详细解释请参考[Appendix 2: target 参数](#appendix-2-target-参数)。

  - availableChains：

    - 限定用户可以选择的区块链网络 ID 列表，如  `['0x405']`(在 Conflux 中 1029 为主网络、1 为测试网)
    - 用户只能在指定的网络列表中进行选择和授权
    - 传空数组表示可选网络是钱包的所有网络

  - scope：

    - 取决于托管钱包提供商提供的服务，决定了授权后能执行的操作种类。应用申请的 scope 需要由托管钱包提供商展示给用户并由用户批准。
    - 以下为推荐托管钱包提供商支持以下 scope。
      - `address` 查看地址（必选）。
      - `sign`。 申请对数据/交易进行签名的权限。当此权限未被用户批准时，签名接口将不可用。可选的，托管钱包提供商可以额外支持下面两个子 scope，这将细化 dapp 申请签名的能力
        - `sign_data`
        - `sign_tx`
    - 可选的，托管钱包提供商可以支持其他 scope，如

      - `phone`
      - `email`
      - `avatar`
      - `openid`
        等

      > `sign` scope 被批准后，并不代表之后发送交易或签名不再需要用户授权，这取决于托管钱包提供商的实现方式。blind_sign

返回值:

- 无返回值
   1. target 为 self 与 blank 时，页面被重定向，无返回值。target 为 iframe 时，无返回值。若授权错误则抛出异常。（TODO: check 这里返回值）
- 失败时抛出异常

示例：

``` js
window.conflux.request({
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
- scope：string[]。用户已批准的 scope，可为[]

##### wallet_revoke

撤销 DAPP 本地与钱包服务器远端相关 token（如果存在）。无参数。无返回值。撤销失败时抛出错误。

> https://www.rfc-editor.org/rfc/rfc7009

#### 资源请求接口

##### cfx_accounts

获取当前连接的账户在当前连接的网络下的地址

参数：无

返回值：string[] 

若已授权，为只包含一个地址的数组，若未授权或者钱包锁定返回[]

##### cfx_sendTransaction

签名并发送交易。

参数：

1. txParams
  - from
  - to
  - data

返回值：字符串

交易哈希。

> 关于此接口的额外讨论见附录3

##### cfx_chainId

获取当前用户授权网络的 chain id

参数：无

返回值：string hex

##### cfx_signTypedData_V4

CIP-23 结构化数据签名

参数：

1. data
   > 参考 [https://github.com/Conflux-Chain/CIPs/blob/2d9fdbdb08f66f705348669a6cd85e2d53509e97/CIPs/cip-23.md](https://github.com/Conflux-Chain/CIPs/blob/2d9fdbdb08f66f705348669a6cd85e2d53509e97/CIPs/cip-23.md)
2. from

返回值：string

十六进制字符串

##### personal_sign

CIP-23 非结构化消息签名

参数：

1. msg
2. from

返回值：string

十六进制字符串

#### 其他钱包功能接口

##### wallet_switchConfluxChain

参数：

1. object
   - chainId： hex字符串

返回值：

无返回值，失败时（不支持的chain）/ 授权未通过时抛出错误。

> EIP-3326

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

| status code | name                |                               |
| ----------- | ------------------- | ----------------------------- |
| 5000        | SDKNotReady         | SDK 或相关依赖未完成初始化    |
| 6000        | ParamsError         | 调用 RPC API 时参数错误       |
| 7000        | RequestError        | 请求错误, 具体信息见  Message  和  data |
| 7101        | ServerInternalError | 钱包服务器内部错误            |
| 7102        | Timeout             | 连接到钱包服务器超时          |

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


## Appendix 1: 托管钱包提供商实现与安全性建议

### 实现

本文档不涉及“实现”与“安全性”的强制性要求，托管钱包提供商可以自行选择实现方式。

作为可选的实现之一，本文档推荐托管钱包提供商基于`OAuth2`进行实现。托管钱包的授权、签名过程可以认为是一种授权协议，而OAuth2是目前使用最为广泛的授权协议之一，有丰富的文档与开源实现，其安全性经过了考验。其标准文档对各类潜在的攻击与安全风险进行了讨论，同时也有BCP（Best Current Practice）文档可作参考。基于OAuth2标准进行实现有助于回避协议设计中的潜在安全问题，也有助于提高协议的互操作性。

> 如果基于OAuth2进行设计，推荐兼容 [OpenID标准](https://openid.net/specs/openid-connect-core-1_0.html)。

如果基于OAuth2进行实现，推荐参考以下要点：

- 基于[Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636.html)授权流程进行实现，该实现可支持纯前端应用的授权。详细说明参考[OAuth 2.0 for Browser-Based Apps](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps)。
- 在SDK内部封装OAuth2相关接口，由SDK管理授权状态，尽量避免向开发者暴露OAuth2细节。
- 注意access token的失效时间。如果需要支持refresh token，建议支持refresh token轮换。

相关推荐文档：

- [The OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749)
- [Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/rfc/rfc7636.html)
- [OAuth 2.0 Security Best Current Practice](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics)
- [OAuth 2.0 for Native Apps](https://www.rfc-editor.org/rfc/rfc8252.html)
- [OAuth 2.0 for Browser-Based Apps](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps) （重点章节 [Token Storage in the Browser](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-token-storage-in-the-browse)， [JavaScript Applications obtaining tokens directly](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-javascript-applications-obt)）
- [OpenID Connect Core 1.0 incorporating errata set 1](https://openid.net/specs/openid-connect-core-1_0.html)

### 安全性建议

无论是基于OAuth2还是自行设计协议，均建议托管钱包提供商重点确认实现中是否存在以下安全问题：

- [Client Impersonation](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-client-impersonation)
- [Cross-Site Request Forgery Protections](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-cross-site-request-forgery-)
- [Threat: Access Token Leak in Browser History](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps#name-threat-access-token-leak-in)
- [Insufficient Redirect URI Validation](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics#name-insufficient-redirect-uri-v)
- 如果使用了iframe形式的授权，参考（[intermediate-iframe](https://developers.google.com/identity/gsi/web/amp/intermediate-iframe?hl=zh-cn)）确认实现中是否存在相关安全风险以及可能影响。

> 更详细的列表参考[实现](#实现)内推荐文档的security considerations 部分

不推荐在生产环境中以远端加载的方式（如CDN）使用 wallet SDK。如果CDN被攻击，相应SDK可能被注入恶意代码。如果需要以这种方式使用SDK，如果无法完全信任远端（CDN）的安全性，应当额外校验SDK完整性。

## Appendix 2: target 参数

[wallet_authorize](#wallet_authorize) 的参数包括

- self
- blank
- iframe

对应用户在哪种窗口中进行授权。self 与 blank 为兼容OAuth2流程的参数，将分别在当前窗口/新窗口打开托管钱包提供商控制的授权页，授权完成后会携带token（code）重定向至指定页面（OAuth2中的`redirect_uri`）参数。

目前在OAuth2协议中，没有标准文件对基于iframe的授权方式进行讨论。如果确实需要实现，推荐基于“中间iframe”的技术进行实现。该技术概览如下：

对于DAPP而言，其需要控制两个非同源域，下面我们分别称之为（1） `dapp.com`： dapp 的业务页面（2） `auth.dapp.com`： 中间iframe需要使用的页面，其中会进行完整的oauth授权流程。

- auth.dapp.com 需要配置X-Frame-Options，仅允许其被嵌入dapp.com
- auth.dapp.com 中进行完整的OAuth2流程
- dapp.com 通过与 auth.dapp.com iframe通信获得资源（地址、签名），但 auth.dapp.com 不暴露授权相关token 给父窗口

> 实现参考：https://developers.google.com/identity/gsi/web/amp/intermediate-iframe?hl=zh-cn

## Appendix 3: 其他讨论

### 是否为 cfx_sendTransaction 添加额外参数

考虑为`cfx_sendTransaction` 添加额外参数`waitForHash`。在签名前提前返回。

调用接口->用户授权->签名是一个耗时较长的异步过程，直到签名完成前都无法拿到交易哈希。通过添加这一参数可以让DAPP更灵活地处理相关逻辑。

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


对应的，wallet_getTransactionStatusById 定义

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

本部分标识了部分可选的接口，如果钱包方要进行实现，推荐参考以下定义进行实现。

##### net_version

参数：无
返回值：string network_id

> 对于DAPP开发者，更推荐使用`cfx_chainId`

##### wallet_userInfo

根据用户批准的 scope 的获取用户信息。

参数: 无

返回值： object

一个对象。取决于已批准的 scope。
