---
cip: 78
title: Correct `is_sponsored` information in receipt
author: <a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s), e.g. (use with the parentheses or triangular brackets): Chenxing Li (<lylcx2007@gmail.com>)
status: Draft
type: Spec Breaking
created: 2021-07-06
---


## Simple Summary

Fix incorrect fields in transaction receipt. 

## Abstract

Currently, the transaction receipt indicates the transaction is not sponsored when its execution fails, even if the sponsor actually pays for the gas fee. Besides, when the sponsor doesn't have enough balance for storage collateral, the sender will afford storage collateral. However, the receipt records that the transaction is sponsored. 

## Motivation

No matter what happens, the "is_sponsored" fields in the transaction receipt should always be consistent with if the sponsor pays. 

## Specification

Before this CIP.
|                     | Not in whitelist            | In whitelist but sponsor cannot afford | Sponsored                       |
| ------------------- | --------------------------- | -------------------------------------- | ------------------------------- |
| **Success**         | Gas: false / Storage: false | Gas: false / **Storage: true**         | Gas: true / Storage: true       |
| **Reverted / Fail** | Gas: false / Storage: false | Gas: false / Storage: false            | **Gas: false / Storage: false** |

After this CIP.
|                     | Not in whitelist            | In whitelist but sponsor cannot afford | Sponsored                       |
| ------------------- | --------------------------- | -------------------------------------- | ------------------------------- |
| **Success**         | Gas: false / Storage: false | Gas: false / **Storage: false**        | Gas: true / Storage: true       |
| **Reverted / Fail** | Gas: false / Storage: false | Gas: false / Storage: false            | **Gas: true / Storage: true**   |

## Rationale

Nothing needs to explain.

## Backwards Compatibility

This CIP changes the transaction receipt. So it is spec breaking. 

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations

Nothing need to consider about the security. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
