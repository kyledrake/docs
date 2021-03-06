+++
title = "MultiCodec"
description = "Compact self-describing codecs"
weight = 4
+++

> Compact self-describing codecs. Save space by using predefined multicodec tables.

## Table of Contents

- [Motivation](#motivation)
- [How does it work? - Protocol Description](#how-does-it-work---protocol-description)
- [Multicodec tables](#multicodec-tables)
  - [Standard multicodec table](#standard-mcp-protocol-table)
- [Implementations](#implementations)
- [FAQ](#faq)

## Motivation

[Multistreams](https://github.com/multiformats/multistream) are self-describing protocol/encoding streams. Multicodec uses an agreed-upon "protocol table". It is designed for use in short strings, such as keys or identifiers (i.e [CID](https://github.com/ipld/cid)).

## Protocol Description - How does the protocol work?

`multicodec` is a _self-describing multiformat_, it wraps other formats with a tiny bit of self-description. A multicodec identifier is both a varint and the code identifying the following data, this means that the most significant bit of every multicodec code is reserved to signal the continuation.

This way, a chunk of data identified by multicodec will look like this:

```sh
<multicodec-varint><encoded-data>
# To reduce the cognitive load, we sometimes might write the same line as:
<mcp><data>
```

Another useful scenario is when using the multicodec-packed as part of the keys to access data, example:

```
# suppose we have a value and a key to retrieve it
"<key>" -> <value>

# we can use multicodec-packed with the key to know what codec the value is in
"<mcp><key>" -> <value>
```

It is worth noting that multicodec-packed works very well in conjunction with [multihash](https://github.com/multiformats/multihash) and [multiaddr](https://github.com/multiformats/multiaddr), as you can prefix those values with a multicodec-packed to tell what they are.

## MulticodecProtocol Tables

Multicodec uses "protocol tables" to agree upon the mapping from one multicodec code (a single varint). These tables can be application specific, though -- like [with](https://github.com/multiformats/multihash) [other](https://github.com/multiformats/multibase) [multiformats](https://github.com/multiformats/multiaddr) -- we will keep a globally agreed upon table with common protocols and formats.

## Multicodec table

The full table can be found at [table.csv](/table.csv) inside this repo.

### Adding new multicodecs to the table

The process to add a new multicodec to the table is the following:

- 1. Fork this repo
- 2. Update the table with the value you want to add
- 3. Submit a Pull Request

This ["first come, first assign"](https://github.com/multiformats/multicodec/pull/16#issuecomment-260146609) policy is a way to assign codes as they are most needed, without increasing the size of the table (and therefore the size of the multicodecs) too rapidly.

## Implementations

- [go](https://github.com/multiformats/go-multicodec/)
- [JavaScript](https://github.com/multiformats/js-multicodec)
- [Python](https://github.com/multiformats/py-multicodec)
- [Haskell](https://github.com/multiformats/haskell-multicodec)
- [Add yours today!](https://github.com/multiformats/multicodec/edit/master/table.csv)

## Multicodec Path, also known as [`multistream`](https://github.com/multiformats/multistream)

Multicodec defines a table for the most common data serialization formats that can be expanded overtime or per application bases, however, in order for two programs to talk with each other, they need to know before hand which table or table extension is being used.

In order to enable self descriptive data formats or streams that can be dynamically described, without the formal set of adding a binary packed code to a table, we have [`multistream`](https://github.com/multiformats/multistream), so that applications can adopt multiple data formats for their streams and with that create different protocols.

## FAQ

> **Q. I have questions on multicodec, not listed here.**

That's not a question. But, have you checked the proper [multicodec FAQ](./README.md#faq)? Maybe your question is answered there. This FAQ is only specifically for multicodec-packed.

> **Q. Why?**

Because [multistream](https://github.com/multiformats/multistream) is too long for identifiers. We needed something shorter.

> **Q. Why varints?**

So that we have no limitation on protocols. Implementation note: you do not need to implement varints until the standard multicodec table has more than 127 functions.

> **Q. What kind of varints?**

An Most Significant Bit unsigned varint, as defined by the [multiformats/unsigned-varint](https://github.com/multiformats/unsigned-varint).

> **Q. Don't we have to agree on a table of protocols?**

Yes, but we already have to agree on what protocols themselves are, so this is not so hard. The table even leaves some room for custom protocol paths, or you can use your own tables. The standard table is only for common things.
