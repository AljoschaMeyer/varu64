# VarU64

A simple variable-length encoding for unsigned 64 bit integers.

## Specification

To decode a VarU64, look at the first byte. If its value is below 248, the value itself is the encoded number. Else, the first byte determines the further `length` of the encoding:

| first byte | number of additional bytes |
|------------|----------------------------|
| 248 | 1 |
| 249 | 2 |
| 250 | 3 |
| 251 | 4 |
| 252 | 5 |
| 253 | 6 |
| 254 | 7 |
| 255 | 8 |

Following the first byte are `length` many bytes. These bytes are the big-endian representation of the encoded number.

## Canonical VarU64

This specification allows different ways to encode the same number, e.g. `0x00` and `0xf800` both decode to zero. To obtain a canonical encoding, we limit those possibilities: Of all possible representations for a number that this scheme admits, the shortest one is its unique, canonical encoding. Canonical decoders must indicate an error if a value uses an encoding that is longer than necessary.

## Variable-Length Encodings of Other Fixed-Size Integers

Analogously to this specification, you can encode signed integers by using big-endian two's complement representation.

The specification can also be adapted to other sizes of integers (up to 2040 bit inclusive), by starting the length table at a different value. For example, the VarI32 encoding for signed 32-bit integers is defined as follows:

To decode a VarI32, look at the first byte. If its (unsigned) value is below 252, the value itself is the encoded number in two's complement. Else, the first byte determines the further `length` of the encoding:

| first byte (unsigned) | number of additional bytes |
|------------|----------------------------|
| 252 | 1 |
| 253 | 2 |
| 254 | 3 |
| 255 | 4 |

Following the first byte are `length` many bytes. These bytes are the big-endian, two's complement representation of the encoded number.

## Non-Zero Unsigned Integers

The VarNonZeroU64 encoding for nonzero 64-bit integers consists of subtracting one from the integer and encoding the resulting number as a regular VarU64. Unsigned integers of other size work analogously.

## Greater-Than `X` Unsigned Integers

Non-zero unsigned integers can be generalized to unsigned integers strictly greater than some number `X`: The VarGtXU64 encoding for 64-bit integer strictly great then `X` consists of subtracting `X` from the integer and encoding the resulting number as a regular VarU64. Unsigned integers of other size work analogously.

## Remarks

This has been inspired by the issues in the [multiformats varint](https://github.com/multiformats/unsigned-varint) repository, in particular issues [#8](https://github.com/multiformats/unsigned-varint/issues/8) and [#12](https://github.com/multiformats/unsigned-varint/issues/12).
