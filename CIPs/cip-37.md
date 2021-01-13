---
cip: 37
title: Introduce Base32 Checksum Addresses
author: bytelaw, Péter Garamvölgyi <peter@conflux-chain.com>
discussions-to: https://github.com/Conflux-Chain/CIPs/issues/37
status: Last Call
type: Database/RPC Breaking
created: 2021-01-07
---

<!--You can leave these HTML comments in your merged CIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new CIPs. Note that a CIP number will be assigned by an editor. When opening a pull request to submit your CIP, please use an abbreviated title in the filename, `CIP-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary

This CIP introduces a new address format to help users distinguish between Conflux and Ethereum addresses.

<!-------------------->

## Abstract

Apart from the regular type-prefixed hex addresses Conflux uses today, we introduce a new base32-encoded address format. Addresses of the new format are derived from the hex-address directly. They contain an easy-to-recognize prefix (e.g. `"cfx"`), an optional address type, and a checksum. Using base32check encoding helps us avoid and detect typos and makes it easy to disambiguate between Conflux addresses and addresses on other blockchains, thus reducing the risk of loss of funds.

An example of the new address format: `cfx:00d2z01m2g4p77n6mddvtaw2k43622daamm1867uk6`.

<!-------------------->

## Motivation

Currently, Conflux and Ethereum addresses are very similar, in many cases they are cross-compatible, i.e. some valid Ethereum addresses are valid Conflux addresses and vice versa. This has led many users to lose their funds when accidentally transferring them to a Conflux address on Ethereum, particularly during cross-chain operations. A new address type that is incompatible with Ethereum would allow tools like Metamask to prevent such erroneous transactions from being submitted to the network.

<!-------------------->

## Specification

*(Parts of this section are based on the Bitcoin Cash [specification](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md).)*

This section defines conversion between the following address types:

- **conflux-hex-address**: a valid, 20-byte, type-prefixed Conflux hex address, e.g. `0x106d49f8505410eb4e671d51f7d96d2c87807b09`. The way this address is derived from public keys is defined by Equation (1) of the protocol [spec](https://confluxnetwork.org/files/Conflux_Protocol_Specification_20201020.pdf). The string representation of this address can optionally use *mixed-case checksum address encoding* ([EIP-55](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md)).

- **conflux-base32-address**: a network-prefixed Conflux base32-checksum address. The address consists of a network-prefix indicating the network on which this address is valid, a colon (`":"`), and a base32-encoded payload indicating the destination of the address and containing a checksum, e.g. `cfx:0086ujfsa1a11uuecwen3xytdmp8f03v140ypk3mxc`. Optionally, the address can contain a list of key value pairs in the format `key=value` between the network-prefix and the payload, separated by colons, e.g. `cfx:type=user:0086ujfsa1a11uuecwen3xytdmp8f03v140ypk3mxc`

<!-------------------->

### Encoding

INPUT: an `addr` (20-byte conflux-hex-address), a `network-id` (4 bytes)

OUTPUT: a conflux-base32-address

1. **Network-prefix**:

```
    match network-id:
        case 1029: "cfx"
        case 1:    "cfxtest"
        case n:    "net[n]"
```

Examples of valid network-prefixes: `"cfx"`, `"cfxtest"`, `"net17"`

Examples of invalid network-prefixes: `"bch"`, `"conflux"`, `"net1"`, `"net1029"`

When presented to users, the prefix may be omitted as it is part of the checksum computation. The checksum ensures that addresses on different networks will remain incompatible, even in the absence of an explicit prefix.

2. [OPTIONAL] **Address-type**:

```
    match addr[0] & 0xf0
        case b00000000: "builtin"
        case b00010000: "user"
        case b10000000: "contract"
```

Examples of valid address-types: `"builtin"`, `"user"`, `"contract"`

Examples of invalid address-types: `"internal"`

3. **Version-byte**:

The version byte's most signficant bit is reserved and must be 0. The 4 next bits indicate the type of address and the 3 least significant bits indicate the size of the hash.

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

4. Base32-encode address:

To create the payload, first, concatenate the `version-byte` with `addr` to get a 21-byte array. Then, encode it left-to-right, mapping each 5-bit sequence to the corresponding ASCII character (see alphabet below). Pad to the right with zero bits to complete any unfinished chunk at the end. In our case, 21-byte payload + 2 bit 0-padding will result in a 34-byte base32-encoded string.

We use the following alphabet: `0123456789abcdefghjkmnprstuvwxyz` (`o`, `i`, `l`, `q` removed).

```
0x00 => 0    0x08 => 8    0x10 => g    0x18 => s
0x01 => 1    0x09 => 9    0x11 => h    0x19 => t
0x02 => 2    0x0a => a    0x12 => j    0x1a => u
0x03 => 3    0x0b => b    0x13 => k    0x1b => v
0x04 => 4    0x0c => c    0x14 => m    0x1c => w
0x05 => 5    0x0d => d    0x15 => n    0x1d => x
0x06 => 6    0x0e => e    0x16 => p    0x1e => y
0x07 => 7    0x0f => f    0x17 => r    0x1f => z
```

4. Calculate checksum: We use the checksum algorithm of Bitcoin Cash, defined [here](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md#checksum).

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
- The lower 5 bits of each character of the prefix. - e.g. "bit..." becomes 2,9,20,...
- A zero for the separator (5 zero bits).
- The payload by chunks of 5 bits. If necessary, the payload is padded to the right with zero bits to complete any unfinished chunk at the end.
- Eight zeros as a "template" for the checksum.

Importantly, the optional `address-type` is **NOT** part of the checksum computation.

The 40-bit number returned by PolyMod is split into eight 5-bit numbers (msb first).
The payload and the checksum are then encoded according to the base32 character table.

5. Concatenate the following parts to get the final address: `[network-prefix]`, `":"`, `[payload]`, `[checksum]`

<!-------------------->

### Decoding

INPUT: a conflux-base32-address

OUTPUT: a conflux-hex-address (20 bytes) and a network-id (4 bytes)

1. Treating the address as an ASCII-string, split at `":"`. If the resulting array has less than 2 items, reject. We treat the first item as `network-prefix-raw`, and the last item as `payload-raw`. Any other items in-between are treated as optional key-value pairs.

2. Parse **network-id**:

```
    match network-prefix-raw
        case "cfx":     1029
        case "cfxtest": 1
        case "cfx1029": reject
        case "cfx1":    reject
        case "cfx[n]":  n if n fits into 4 bytes
        otherwise:      reject
```

3. Validate payload:

    - If the length of `payload-raw` is not `42`, reject.
    - If `payload-raw` contains both lowercase and uppercase characters, reject.
    - If `payload-raw` contains invalid characters (not in the alphabet), reject.

4. Validate checksum.

To verify a base32-formatted address, the input data (list of integers) for the PolyMod function is assembled from these parts:
- The lower 5 bits of each character of the prefix.
- A zero for the separator (5 zero bits).
- Each base32 char of the payload mapped to its respective number.

If PolyMod returns non-zero, then the address was broken, reject.

5. Convert `payload-raw` into 8-bit array, split into `version-byte` and `payload`.

6. Validate `version-byte`: If `version-byte` is **NOT** `0x00`, reject.

7. Validate **address-type**  (if provided):

    - If `payload[0] & 0xf0` is `b00000000` and `address-type` is **NOT** `"builtin"`: reject.
    - If `payload[0] & 0xf0` is `b00010000` and `address-type` is **NOT** `"user"`: reject.
    - If `payload[0] & 0xf0` is `b10000000` and `address-type` is **NOT** `"contract"`: reject.

Return `(payload, network-id)`.

*(Note: Conflux nodes are expected to validate `network-id` against their local chain id and reject addresses that do not match. This check is not part of this specification.)*

<!-------------------->

### Example

```
encode(0x106d49f8505410eb4e671d51f7d96d2c87807b09, 1029, true)

1. network-prefix: 1029 => "cfx"
2. address-type: "user"
3. version-byte: 0x00
4. payload: [0x00, 0x10, 0x6d, 0x49, 0xf8, 0x50, 0x54, 0x10, 0xeb, 0x4e, 0x67, 0x1d, 0x51, 0xf7, 0xd9, 0x6d, 0x2c, 0x87, 0x80, 0x7b, 0x09]
   5-bit parts: [0x00, 0x00, 0x08, 0x06, 0x1a, 0x12, 0x0f, 0x18, 0xa, 0x1, 0xa, 0x1, 0x1, 0x1a, 0x1a, 0xe, 0xc, 0x1c, 0xe, 0x15, 0x03, 0x1d, 0x1e, 0x19, 0x0d, 0x14, 0x16, 0x08, 0x0f, 0x00, 0x03, 0x1b, 0x01, 0x04]
   base32-encoded: "0086ujfsa1a11uuecwen3xytdmp8f03v14"
5. base32-encoded checksum: "0ypk3mxc"
6. concatenated result: "cfx:user:0086ujfsa1a11uuecwen3xytdmp8f03v140ypk3mxc"
```

<!-------------------->

## Rationale

**network-prefix**: We chose an easy-to-recognize network-prefix (`cfx`) so that users can simply tell Conflux addresses from addresses on other blockchains. We also differentiate between addresses based on the Conflux chain they are expected to be used on (e.g. mainnet vs testnet). This way, we can make it harder for users to accidentally submit testnet or private chain transactions on the mainnet.

**Address-type**: Conflux-hex-addresses have the desirable property that users can tell whether an address is an externally owned account (`0x1`) or a contract account (`0x8`) just by looking at the address. While this information is present in base32-encoded addresses, it is not so obvious. To keep this property, we allow for an optional human-readable address-type to be included in base32 addresses.

**Version-byte**: The version byte is kept so that Conflux addresses can be processed by existing bch-base32 implementations. The version-byte is currently unused and it is reserved for future extensions. The version-byte is validated during decoding so that we prevent users from generating several (valid) base32-address aliases for the same Conflux address.

<!-------------------->

## Backwards Compatibility

Hex-addresses and base32-addresses encode the same underlying byte sequence, so they are equivalent from the perspective of consensus (e.g. address-derivation for contracts), and transaction signing.

This change requires updating various tools (wallets, IDEs, SDKs, etc.) and dapps in the Conflux ecosystem so that they can interpret base32-addresses.

<!-------------------->

## Test Cases

```
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, mainnet, false) = cfx:022xg0j5vg1fba4nh7gz372we6740puptms36cm58c
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, testnet, false) = cfxtest:022xg0j5vg1fba4nh7gz372we6740puptmj8nwjfc6
encode(0x85d80245dc02f5a89589e1f19c5c718e405b56cd, testnet, true)  = cfxtest:contract:022xg0j5vg1fba4nh7gz372we6740puptmj8nwjfc6

encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, mainnet, false) = cfx:00d2z01m2g4p77n6mddvtaw2k43622daamm1867uk6
encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, testnet, false) = cfxtest:00d2z01m2g4p77n6mddvtaw2k43622daamyavp1grc
encode(0x1a2f80341409639ea6a35bbcab8299066109aa55, testnet, true)  = cfxtest:user:00d2z01m2g4p77n6mddvtaw2k43622daamyavp1grc

encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, mainnet, false) = cfx:00cwegpesgntwkrz7e2cvvedwbusmdrm9wdsdks47x
encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, testnet, false) = cfxtest:00cwegpesgntwkrz7e2cvvedwbusmdrm9w7ky3ye3r
encode(0x19c742cec42b9e4eff3b84cdedcde2f58a36f44f, testnet, true)  = cfxtest:user:00cwegpesgntwkrz7e2cvvedwbusmdrm9w7ky3ye3r

encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, mainnet, false) = cfx:0229g2mmv57n9b1ka44kjf08t1ka46sv1s1vpcf0wh
encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, testnet, false) = cfxtest:0229g2mmv57n9b1ka44kjf08t1ka46sv1sbg5w9asv
encode(0x84980a94d94f54ac335109393c08c866a21b1b0e, testnet, true)  = cfxtest:contract:0229g2mmv57n9b1ka44kjf08t1ka46sv1sbg5w9asv

encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, mainnet, false) = cfx:00edyeb9mgmaem5skctwz4y9cnge5f8ru42rbphk8r
encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, testnet, false) = cfxtest:00edyeb9mgmaem5skctwz4y9cnge5f8ru48ws6rtcx
encode(0x1cdf3969a428a750b89b33cf93c96560e2bd17d1, testnet, true)  = cfxtest:user:00edyeb9mgmaem5skctwz4y9cnge5f8ru48ws6rtcx

encode(0x0888000000000000000000000000000000000002, mainnet, false) = cfx:0048g00000000000000000000000000008djg2z8b1
encode(0x0888000000000000000000000000000000000002, testnet, false) = cfxtest:0048g000000000000000000000000000087t3jt2fb
encode(0x0888000000000000000000000000000000000002, testnet, true)  = cfxtest:builtin:0048g000000000000000000000000000087t3jt2fb
```

More test cases can be found in [conflux-chain#2034](https://github.com/Conflux-Chain/conflux-rust/pull/2034).

<!-------------------->

## Implementation

This address scheme has been implemented in [conflux-chain#2034](https://github.com/Conflux-Chain/conflux-rust/pull/2034).

<!-------------------->

## Security Considerations

TBD

<!-------------------->

## References

Bitcoin Wiki: Base58Check encoding \[[link](https://en.bitcoin.it/wiki/Base58Check_encoding)\]

IETF: The Base58 Encoding Scheme \[[link](https://tools.ietf.org/id/draft-msporny-base58-01.html)\]

EIPs #77: Safer Ethereum Address Format \[[link](https://github.com/ethereum/eips/issues/77)\]

Tron Documentation: 3.4 Address Format \[[link](https://developers.tron.network/docs/account#address-format)\]

Tezos Base58 Encoding with Prefix (eztz codebase) \[[link](https://github.com/TezTech/eztz/blob/master/src/main.js#L15)\]

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
