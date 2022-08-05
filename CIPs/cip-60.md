---
cip: 60
title: Log VM Error in Tracer 
author: Chenxing Li (@ChenxingLi)
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/61
status: Final
type: Database Breaking
created: 2021-02-04
---

## Simple Summary
Log VM error (e.g., internal-contract error, invalid opcode) in trace.

## Abstract
Log VM error (e.g., internal-contract error, invalid opcode) in block trace (the return of `trace_block` rpc function).


## Motivation
The VM tracer will log execution result currently. However, if the execution fails due to internal-contract error, invalid opcode etc, the reason will not logged in VM. This may bring trouble in debugging contract.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Conflux platforms ([conflux-rust](https://github.com/Conflux-Chain/conflux-rust)).-->

### Parameters

Currently, the definition of `MessageCallResult` is 

```
pub enum MessageCallResult {
    /// Returned when message call was successful.
    /// Contains gas left and output data.
    Success(U256, ReturnData),
    /// Returned when message call failed.
    /// VM doesn't have to know the reason.
    Failed,
    /// Returned when message call was reverted.
    /// Contains gas left and output data.
    Reverted(U256, ReturnData),
}
```

It should be changed to 

```
pub enum MessageCallResult {
    /// Returned when message call was successful.
    /// Contains gas left and output data.
    Success(U256, ReturnData),
    /// Returned when message call failed.
    /// VM doesn't have to know the reason.
    Failed(vm::Error),
    /// Returned when message call was reverted.
    /// Contains gas left and output data.
    Reverted(U256, ReturnData),
}
```

And so does `ContractCreateResult`.
## Rationale

TBA.

## Backwards Compatibility

This CIP changes the blocks' trace, which is logged in database. So it is database breaking, but it is not spec breaking. Because the result will not influence block header. 

## Test Cases

TBA.

## Implementation

TBA.

## Security Considerations

No security considerations. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
