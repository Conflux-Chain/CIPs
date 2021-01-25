---
cip: 37
title: Introduce Base32 Checksum Addresses
author: bytelaw, Péter Garamvölgyi <peter@conflux-chain.com>
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/37
status: Final
type: Database/RPC Breaking
created: 2021-01-07
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This CIP introduces a new address format to help users distinguish between Conflux and Ethereum addresses.

<!-------------------->

## Abstract

Apart from the regular type-prefixed hex addresses Conflux uses today, we introduce a new base32-encoded address format. Addresses of the new format are derived from the hex-address directly. They contain an easy-to-recognize prefix (e.g. `"cfx"`), an optional address type, and a checksum. Using base32check encoding helps us avoid and detect typos and makes it easy to disambiguate between Conflux addresses and addresses on other blockchains, thus reducing the risk of loss of funds.

An example of the new address format: `cfx:aarc9abycue0hhzgyrr53m6cxedgccrmmyybjgh4xg`.

<!-------------------->

## Motivation

Currently, Conflux and Ethereum addresses are very similar, in many cases they are cross-compatible, i.e. some valid Ethereum addresses are valid Conflux addresses and vice versa. This has led many users to lose their funds when accidentally transferring them to a Conflux address on Ethereum, particularly during cross-chain operations. A new address type that is incompatible with Ethereum would allow tools like Metamask to prevent such erroneous transactions from being submitted to the network.

<!-------------------->

## Specification

*(Parts of this section are based on the Bitcoin Cash [specification](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md).)*

This section defines conversion between the following address types:

- **conflux-hex-address**: a valid, 20-byte, type-prefixed Conflux hex address, e.g. `0x106d49f8505410eb4e671d51f7d96d2c87807b09`. The way this address is derived from public keys is defined by Equation (1) of the protocol [spec](https://confluxnetwork.org/files/Conflux_Protocol_Specification_20201020.pdf). The string representation of this address can optionally use *mixed-case checksum address encoding* ([EIP-55](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md)).

- **conflux-base32-address**: a network-prefixed Conflux base32-checksum address. The address consists of a network-prefix indicating the network on which this address is valid, a colon (`":"`), and a base32-encoded payload indicating the destination of the address and containing a checksum, e.g. `cfx:aarc9abycue0hhzgyrr53m6cxedgccrmmyybjgh4xg`. Optionally, the address can contain a list of key value pairs in the format `key.value` between the network-prefix and the payload, separated by colons, e.g. `cfx:type.user:aarc9abycue0hhzgyrr53m6cxedgccrmmyybjgh4xg`

<!-------------------->

### Encoding

INPUT: `addr` (a conflux-hex-address), `network-prefix` (string)

OUTPUT: a conflux-base32-address

`network-prefix` is one of the following values: `"cfx"` (mainnet, corresponds to network-id 1029), `"cfxtest"` (testnet, corresponds to network-id 1), `"net[n]"` where `n != 1, 1029` (private Conflux network)

Examples of valid network-prefixes: `"cfx"`, `"cfxtest"`, `"net17"`

Examples of invalid network-prefixes: `"bch"`, `"conflux"`, `"net1"`, `"net1029"`

1. [OPTIONAL] **Address-type**: For the null address (`0x0000000000000000000000000000000000000000`), address-type must be `"type.null"`. Otherwise,

```
    match addr[0] & 0xf0
        case b00000000: "type.builtin"
        case b00010000: "type.user"
        case b10000000: "type.contract"
```

2. **Version-byte**:

The version byte's most significant bit is reserved and must be 0. The 4 next bits indicate the type of address and the 3 least significant bits indicate the size of the hash.

| Type bits |      Meaning      | Version byte value |
| --------: | :---------------: | -----------------: |
|         0 |      Conflux      |                  0 |

Further types might be added as new features are added.

| Size bits | Hash size in bits |
| --------: | ----------------: |
|         0 |               160 |
|         1 |               192 |
|         2 |               224 |
|         3 |               256 |
|         4 |               320 |
|         5 |               384 |
|         6 |               448 |
|         7 |               512 |

By encoding the size of the hash in the version field, we ensure that it is possible to check that the length of the address is correct.

3. **Base32-encode address**:

To create the payload, first, concatenate the `version-byte` with `addr` to get a 21-byte array. Then, encode it left-to-right, mapping each 5-bit sequence to the corresponding ASCII character (see alphabet below). Pad to the right with zero bits to complete any unfinished chunk at the end. In our case, 21-byte payload + 2 bit 0-padding will result in a 34-byte base32-encoded string.

We use the following alphabet: `0123456789abcdefghjkmnprstuvwxyz` (`o`, `i`, `l`, `q` removed).

```
0x00 => a    0x08 => j    0x10 => u    0x18 => 2
0x01 => b    0x09 => k    0x11 => v    0x19 => 3
0x02 => c    0x0a => m    0x12 => w    0x1a => 4
0x03 => d    0x0b => n    0x13 => x    0x1b => 5
0x04 => e    0x0c => p    0x14 => y    0x1c => 6
0x05 => f    0x0d => r    0x15 => z    0x1d => 7
0x06 => g    0x0e => s    0x16 => 0    0x1e => 8
0x07 => h    0x0f => t    0x17 => 1    0x1f => 9
```

4. **Calculate checksum**: We use the checksum algorithm of Bitcoin Cash, defined [here](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md#checksum).

```cpp
uint64_t PolyMod(const data &v) {
    uint64_t c = 1;
    for (uint8_t d : v) {
        uint8_t c0 = c >> 35;
        c = ((c & 0x07ffffffff) << 5) ^ d;

        if (c0 & 0x01) c ^= 0x98f2bc8e61;
        if (c0 & 0x02) c ^= 0x79b76d99e2;
        if (c0 & 0x04) c ^= 0xf33e5fb3c4;
        if (c0 & 0x08) c ^= 0xae2eabe2a8;
        if (c0 & 0x10) c ^= 0x1e4f43e470;
    }

    return c ^ 1;
}
```

The checksum is calculated over the following data:
- The lower 5 bits of each character of the `network-prefix`. - e.g. `"cfx..."` becomes `0x03, 0x06, 0x18, ...`
- A zero for the separator (5 zero bits).
- The payload by chunks of 5 bits. If necessary, the payload is padded to the right with zero bits to complete any unfinished chunk at the end.
- Eight zeros as a "template" for the checksum.

Importantly, optional fields (like `address-type`) are **NOT** part of the checksum computation.

The 40-bit number returned by PolyMod is split into eight 5-bit numbers (msb first).
The payload and the checksum are then encoded according to the base32 character table.

5. Concatenate the following parts to get the final address: `[network-prefix]`, `":"`, `[payload]`, `[checksum]`
    - Optionally, **address-type** can also be included: `[network-prefix]`, `":"`, `[address-type]`, `":"`, `[payload]`, `[checksum]`

<!-------------------->

### Decoding

INPUT: a conflux-base32-address

OUTPUT: a conflux-hex-address and a network-prefix

1. Treat the address as an ASCII-string. If it is mixed case (contains both lowercase and uppercase letters), reject. Convert address to lowercase. Split at `":"`. If the resulting array has less than 2 items, reject. Treat the first item as `network-prefix`, and the last item as `payload-raw`. Any other items in-between should be treated as optional key-value pairs.

2. Validate `network-prefix` according to the specification above.

3. Convert the base32-formatted `payload-raw` into bytes using the alphabet and verify the checksum.

If `payload-raw` contains any invalid characters (not in the alphabet), reject.

To verify a base32-formatted address, the input data (list of integers) for the PolyMod function is assembled from these parts:
- The lower 5 bits of each character of the prefix.
- A zero for the separator (5 zero bits).
- Each base32 char of the payload mapped to its respective number.

If PolyMod returns non-zero, then the address was broken, reject.

4. Convert `payload-raw` into an 8-bit array, split into `version-byte` and `addr-bytes`.

5. Verify the type and size bits in the `version-byte`. Unknown versions should be rejected.

6. Verify optional fields:
    - If the optional fields contain `type.*`: Verify the address-type according to the specification above.
    - Unknown options (options other than `type.*`) should be ignored.

Return `(addr-bytes, network-prefix)`.

*(Note: Conflux nodes are expected to validate `network-prefix` against their local network id and reject addresses that do not match. This check is not part of this specification.)*

<!-------------------->

### Example

```
encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, "cfx")

1. address-type: "type.user"
2. version-byte: 0x00
3. payload: [0x00, 0x1a, 0x2f, 0x80, 0x34, 0x14, 0x09, 0x63, 0x9e, 0xa6, 0xa3, 0x5b, 0xbc, 0xab, 0x82, 0x99, 0x06, 0x61, 0x09, 0xaa, 0x55]
   5-bit parts: [0x00, 0x00, 0x0d, 0x02, 0x1f, 0x00, 0x01, 0x14, 0x02, 0x10, 0x04, 0x16, 0x07, 0x07, 0x15, 0x06, 0x14, 0x0d, 0x0d, 0x1b, 0x19, 0x0a, 0x1c, 0x02, 0x13, 0x04, 0x03, 0x06, 0x02, 0x02, 0x0d, 0x0a, 0x0a, 0x14]
   base32-encoded: "aarc9abycue0hhzgyrr53m6cxedgccrmmy"
4. checksum input: [0x03, 0x06, 0x18, 0x00, 0x00, 0x00, 0x0d, 0x02, 0x1f, 0x00, 0x01, 0x14, 0x02, 0x10, 0x04, 0x16, 0x07, 0x07, 0x15, 0x06, 0x14, 0x0d, 0x0d, 0x1b, 0x19, 0x0a, 0x1c, 0x02, 0x13, 0x04, 0x03, 0x06, 0x02, 0x02, 0x0d, 0x0a, 0x0a, 0x14, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00]
   checksum output: 688543492710
   checksum string: "ybjgh4xg"
5. concatenated result: "cfx:type.user:aarc9abycue0hhzgyrr53m6cxedgccrmmyybjgh4xg"
```

<!-------------------->

## Rationale

**network-prefix**: We chose an easy-to-recognize network-prefix (`cfx`) so that users can simply tell Conflux addresses from addresses on other blockchains. We also differentiate between addresses based on the Conflux chain they are expected to be used on (e.g. mainnet vs testnet). This way, we can make it harder for users to accidentally submit testnet or private chain transactions on the mainnet.

**Address-type**: Conflux-hex-addresses have the desirable property that users can tell whether an address is an externally owned account (`0x1`) or a contract account (`0x8`) just by looking at the address. While this information is present in base32-encoded addresses, it is not so obvious. To keep this property, we allow for an optional human-readable address-type to be included in base32 addresses.

**Version-byte**: The version byte is kept so that Conflux addresses can be processed by existing bch-base32 implementations. The version-byte is currently unused and it is reserved for future extensions. The version-byte is validated during decoding so that we prevent users from generating several (valid) base32-address aliases for the same Conflux address.

**Dot as key-value separator**: For optional parameters, we choose the format `key.value` instead of the more intuitive `key=value` as this way addresses can be efficiently encoded in QR codes using the alphanumeric mode.

<!-------------------->

## Backwards Compatibility

Hex-addresses and base32-addresses encode the same underlying byte sequence, so they are equivalent from the perspective of consensus (e.g. address-derivation for contracts), and transaction signing.

This change requires updating various tools (wallets, IDEs, SDKs, etc.) and dapps in the Conflux ecosystem so that they can interpret base32-addresses.

<!-------------------->

## Test Cases

```
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, "cfx") = cfx:acc7uawf5ubtnmezvhu9dhc6sghea0403y2dgpyfjp
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, "cfxtest") = cfxtest:acc7uawf5ubtnmezvhu9dhc6sghea0403ywjz6wtpg
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, "cfxtest") = cfxtest:type.contract:acc7uawf5ubtnmezvhu9dhc6sghea0403ywjz6wtpg

encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, "cfx") = cfx:aarc9abycue0hhzgyrr53m6cxedgccrmmyybjgh4xg
encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, "cfxtest") = cfxtest:aarc9abycue0hhzgyrr53m6cxedgccrmmy8m50bu1p
encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, "cfxtest") = cfxtest:type.user:aarc9abycue0hhzgyrr53m6cxedgccrmmy8m50bu1p

encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, "cfx") = cfx:aap6su0s2uz36x19hscp55sr6n42yr1yk6r2rx2eh7
encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, "cfxtest") = cfxtest:aap6su0s2uz36x19hscp55sr6n42yr1yk6hx8d8sd1
encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, "cfxtest") = cfxtest:type.user:aap6su0s2uz36x19hscp55sr6n42yr1yk6hx8d8sd1

encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, "cfx") = cfx:acckucyy5fhzknbxmeexwtaj3bxmeg25b2b50pta6v
encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, "cfxtest") = cfxtest:acckucyy5fhzknbxmeexwtaj3bxmeg25b2nuf6km25
encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, "cfxtest") = cfxtest:type.contract:acckucyy5fhzknbxmeexwtaj3bxmeg25b2nuf6km25

encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, "cfx") = cfx:aasr8snkyuymsyf2xp369e8kpzusftj14ec1n0vxj1
encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, "cfxtest") = cfxtest:aasr8snkyuymsyf2xp369e8kpzusftj14ej62g13p7
encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, "cfxtest") = cfxtest:type.user:aasr8snkyuymsyf2xp369e8kpzusftj14ej62g13p7

encode(0x0888000000000000000000000000000000000002, "cfx") = cfx:aaejuaaaaaaaaaaaaaaaaaaaaaaaaaaaajrwuc9jnb
encode(0x0888000000000000000000000000000000000002, "cfxtest") = cfxtest:aaejuaaaaaaaaaaaaaaaaaaaaaaaaaaaajh3dw3ctn
encode(0x0888000000000000000000000000000000000002, "cfxtest") = cfxtest:type.builtin:aaejuaaaaaaaaaaaaaaaaaaaaaaaaaaaajh3dw3ctn
```

More test cases can be found in [conflux-chain#2034](https://github.com/Conflux-Chain/conflux-rust/pull/2034).

<!-------------------->

## Implementation

This address scheme has been implemented in [conflux-chain#2034](https://github.com/Conflux-Chain/conflux-rust/pull/2034).

<!-------------------->

## Security Considerations

This proposal has no obvious security implications.

<!-------------------->

## References

Bitcoin Cash Specification \[[link](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md)\]

EIPs #77: Safer Ethereum Address Format \[[link](https://github.com/ethereum/eips/issues/77)\]

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
