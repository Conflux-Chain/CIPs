---
cip: <to be assigned>
title: Meta: Tanzanite.
author: Peilun (@peilun-conflux)
discussions-to: https://forum.conflux.fun/t/topic/4249/68
status: Draft
type: Spec Breaking
created: 2020-12-08
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

This specifies the changes included in the hard fork named Tanzanite.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

* Codename: Tanzanite
* Assigned version (first element in `custom`): `[1]`
* Activation: 
    * Block height >= 3,615,000 on Mainnet
    * Block height >= 4,990,000 on Testnet
    * Block height >= 0 on future testnets
* Included CIPs:
    * CIP-38 (Reduce block base reward to 2 CFX)
    * CIP-39 (Use the custom field for incompatible upgrade)
## Security Considerations
<!--All CIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. CIP submissions missing the "Security Considerations" section will be rejected. a CIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
Discussed in CIP-39.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
