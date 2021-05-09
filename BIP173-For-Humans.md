## Address Constraints
```
address   => a..90 characters in the form [prefix][separator][payload]
prefix    -> 1..p characters from { '!'..'~' }
separator -> '1'
payload   => (version)(program)(checksum)
version   -> 1 base32 value from { 0 .. 16 }
program   -> 4..64 base32 values (converted from 2..40 bytes)
checksum  -> 6 base32 values
```
The minimum address length `a` is deduced from `1 + 1 + 1 + 4 + 6 = 13`.

> There are several BIP173 "bech32" test vectors that exclude the version. These are not valid addresses.

The maximum prefix length `p` is deduced from `90 - 1 - 1 - 4 - 6 = 78`.

> There are BIP173 "bech32" test vectors that exclude the version and therefore overstate the maximum prefix length. These are not valid addresses.

The maximum program length is deduced from `90 - a = 77`, but is further limited to `4..64` (2-40 bytes) by [BIP141](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki).

The payload length ranges from `1 + 4 + 6 = 11` to `1 + 64 + 6 = 71`. 

> It is not necessary to enforce the deduced length limits, as these follow from the others. However it may be useful in providing more detailed parse feedback.

Constraints on the witness version and program are provided by BIP141. All valid versions must be supported despite lack of semantic validation for the program of undefined versions. The program should be validated for known witness versions.

> Libbitcoin provides a "strict" parsing option which rejects addresses with unknown witness programs.

BIP141 explicitly limits programs to between 2 and 40 bytes, however this is not a necessary limit of the above address parsing.

> Semantic evaluation of the witness version and program is much more straightforward once the program is converted to bytes.

Prefixes other than "bc" and "tb" are considered invalid.

> There are many other prefixes in widespread use. Libbitcoin supports construction with any otherwise valid prefix and provides a "strict" parsing option which limits prefix validation to "bc" and "tb". Given that the prefix maximum length is not otherwise limited, addresses may be a full 90 characters, and given treatment of the minimum prefix length as one character, the minimum address length is 13. Strict parsing limits addresses to between 14 and 74 characters by adding the two character prefix constraint.

Case is ignored, but mixed case is considered invalid.

> Apart from the explicit mixed case constraint, mixed case addresses would be entirely equivalent. Addresses are required to be created as lower case, however this is of no consequence given that fully upper or lower addresses are equally valid.

## Base32 Encoding
Base32 characters have the following 1:1 mapping to/from base32 values. Only the payload is base32 encoded in an address, the prefix and separator are merely string concatenated with the encoded payload.

> Upper and lower case characters are mapped to the same values, so single-casing simplifies implementation.

```
numbers   => {'0', 15}, {'2', 10}, {'3', 17}, {'4', 21}, {'5', 20}, {'6', 26}, {'7', 30}, {'8',  7}, {'9',  5}
letters   => {'a', 29}, {'c', 24}, {'d', 13}, {'e', 25}, {'f',  9}, {'g',  8}, {'h', 23}, {'j', 18}, {'k', 22},
             {'l', 31}, {'m', 27}, {'n', 19}, {'p',  1}, {'q',  0}, {'r',  3}, {'s', 16}, {'t', 11}, {'u', 28},
             {'v', 12}, {'w', 14}, {'x',  6}, {'y',  4}, {'z',  2}
```

> Base32 is a simple generic mapping which Libbitcoin isolates from address manipulation and checksum computation. Libbitcoin provides base32 functions for encoding/decoding base32 values to/from characters, with overloads for byte encoding/decoding, "bech32" functions for checksum computation, and the `witness_address` class for address manipulation.

```cpp
constexpr char encode[] = "qpzry9x8gf2tvdw0s3jn54khce6mua7l";
constexpr uint8_t decode[] =
{
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    15,   0xff, 10,   17,   21,   20,   26,   30,
    7,    5,    0xff, 0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 29,   0xff, 24,   13,   25,   9,    8,
    23,   0xff, 18,   22,   31,   27,   19,   0xff,
    1,    0,    3,    16,   11,   28,   12,   14,
    6,    4,    2,    0xff, 0xff, 0xff, 0xff, 0xff,
    0xff, 29,   0xff, 24,   13,   25,   9,    8,
    23,   0xff, 18,   22,   31,   27,   19,   0xff,
    1,    0,    3,    16,   11,   28,   12,   14,
    6,    4,    2,    0xff, 0xff, 0xff, 0xff, 0xff
};

std::string encode_base32(const base32_chunk& data)
{
    std::string out;
    out.reserve(data.size());

    for (auto value: data)
        out.push_back(encode[value]);

    return out;
}

bool decode_base32(base32_chunk& out, const std::string& in)
{
    out.reserve(in.size());

    for (auto character: in)
    {
        const auto value = decode[character];

        if (value == 0xff)
            return false;

        out.push_back(value);
    }

    return true;
}
```

## Program Conversion
Program conversion from bytes to/from base32 values follows a linear bit mapping, shown with most significant bytes and bits to the left.

> Only the program can be converted in this manner.
```
[11111111][11111111] => [11111][11111][11111][1xxxx]
[11111][11111][11111][1xxxx] => [11111111][11111111][xxxxpppp]
```
The 8-to-5 bit mapping pads the last value with 1..4 zero bits (x). Multiples of 5 bytes will not be padded.

The 5-to-8 bit mapping trims 1..4 bits (which must be zero). When `(source.size() * 5) % 8` is non-zero, the last source value was padded.

> When writing whole bytes from source, the last byte, e.g. `[xxxxpppp]`, will either be unpadded or *all* padding (and must be dropped). If size indicates source padding, and the last byte is non-zero, the source was not converted properly from bytes. This must be tested in order to invalidate malformed addresses.

While [BIP173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) program conversion is often exceedingly [opaque](https://github.com/sipa/bech32/blob/master/ref/c%2B%2B/segwit_addr.cpp#L33-L54), it is really as simple as this.

> It is often the case that when manipulating bits on arbitrary byte boundaries, code becomes exceedingly complex and fragile. Often the solution is to use a bit stream reader and/or writer. This eliminates what is essentially an [impedance mismatch](https://en.wikipedia.org/wiki/Object%E2%80%93relational_impedance_mismatch), reducing the problem to its essence (e.g. "read 8, write 5" or "read 5, write 8"). Bit twiddling is necessary when working in assembler or C, but in C++ this the antipattern of [accidental complexity](https://en.wikipedia.org/wiki/No_Silver_Bullet).

```cpp
    // 8-to-5 conversion snippet
    while (!bit_reader.is_exhausted())
        out.push_back(bit_reader.read_bits(5));
```
```cpp
    // 5-to-8 conversion snippet
    for (const auto& value: data)
        bit_writer.write_bits(value, 5);

    // If padded and valid 8-to-5 conversion, drop pad byte, clear if invalid.
    if ((data.size() * 5) % 8 != 0)
        out.resize(out.back() == 0x00 ? out.size() - 1u : 0);
```

## Checksum Computation
The checksum is computed over base32 values and compared with `bech32_constant(...)` value for validation. This is a deviation from BIP173 required by [BIP350](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki).

The input to creating or validating a checksum is `(prefix)(version)(program)`. Utilities for expanding the string prefix (for checksum computation) and the integer checksum to base32 (for address incorporation) are also shown below.

> BIP173 test vector checksums fail under BIP350 where their version is non-zero.

```cpp
typedef boost::multiprecision::number<
    boost::multiprecision::cpp_int_backend<5, 5,
    boost::multiprecision::unsigned_magnitude,
    boost::multiprecision::unchecked, void>> uint5_t;
typedef std::vector<uint5_t> base32_chunk;

const size_t bech32_checksum_size = 6;

size_t bech32_expanded_prefix_size(const std::string& prefix)
{
    return 2u * prefix.size() + 1u;
}

void bech32_expand_prefix(base32_chunk& out, const std::string& prefix)
{
    const auto lower = ascii_to_lower(prefix);
    const auto size = lower.size();
    out[size] = 0x00;

    for (size_t index = 0; index < size; ++index)
    {
        char character = lower[index];
        out[index] = character >> 5;
        out[size + 1u + index] = character;
    }
}

void bech32_expand_checksum(base32_chunk& out, uint32_t checksum)
{
    auto index = out.size() - bech32_checksum_size;

    // The top two bits of the 32 bit checksum are discarded.
    out[index++] = (checksum >> 25);
    out[index++] = (checksum >> 20);
    out[index++] = (checksum >> 15);
    out[index++] = (checksum >> 10);
    out[index++] = (checksum >> 5);
    out[index++] = (checksum >> 0);
}

uint32_t bech32_checksum(const base32_chunk& data)
{
    static const uint32_t generator[] =
    {
        0x3b6a57b2, 0x26508e6d, 0x1ea119fa, 0x3d4233dd, 0x2a1462b3
    };

    uint32_t checksum = 1;

    for (const auto& value: data)
    {
        const uint32_t coeficient = (checksum >> 25);
        checksum = ((checksum & 0x1ffffff) << 5) ^ value;

        if (coeficient & 0x01) checksum ^= generator[0];
        if (coeficient & 0x02) checksum ^= generator[1];
        if (coeficient & 0x04) checksum ^= generator[2];
        if (coeficient & 0x08) checksum ^= generator[3];
        if (coeficient & 0x10) checksum ^= generator[4];
    }

    return checksum;
}

// BIP173: All versions use 0x00000001 (bech32).
// BIP350: Nonzero versions use 0x2bc830a3 (bech32m).
uint32_t bech32_constant(uint8_t version)
{
    return version == 0 ? 0x00000001 : 0x2bc830a3;
}
```