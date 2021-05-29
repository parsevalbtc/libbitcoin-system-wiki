## Background
Libbitcoin support for mnemonic wallet seed encoding began with [Electrum](https://electrum.org/) v1. Later came [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki), driven by the fine folks behind [Trezor](https://wiki.trezor.io/), which we believed Electrum was adopting. When the fine folks behind Electrum [decided against BIP39](https://electrum.readthedocs.io/en/latest/seedphrase.html#motivation), we found ourselves with three implementations. We had dropped Electrum v1 in the expectation that BIP39 would become sufficient. Later we added Electrum but found it necessary to also restore Electrum v1. It is not possible to properly implement Electrum mnemonic support without also implementing Electrum v1 and BIP39 mnemonics.

An overhaul of our mnemonic implementations was well overdue. What was anticipated to require one week required over a month of full time work. Test coverage is nearly complete and it will be merged soon. Before I forget the various lessons learned, I decided to write them down here. The information is all out there, somewhere. But ultimately it required digging through a lot of Python and C code. Wallet seeds are not something for a developer to take lightly, and code is always authoritative. Eventually I found myself sifting through Python internals, a deeper rabbit hole than I expected.

I will state for the record that I truly appreciate both Electrum and Trezor. Otherwise I would not have spent the time to provide comprehensive support for all three of these encodings. These observations are provided for my own record and to possibly aid others who may at some point find themselves in that same rabbit hole. When one goes this deep into implementation, interesting discoveries abound.

## Terminology
### Language
A [natural language](https://en.wikipedia.org/wiki/Natural_language).
> Libbitcoin refers to a languages by [IANA subtag](https://www.iana.org/assignments/language-subtag-registry/language-subtag-registry), each considered globally-unique.
### Dictionary
A `dictionary` is a standard set of reference tokens of a single language.
> Token (linguistics) : an individual occurrence of a linguistic unit in speech or writing.

> There may be more than one dictionary per language.
### Word
A `word` is a dictionary token.
> Non-dictionary word encoding is possible in Electrum.
### Mnemonic
A `mnemonic` is an ordered set of words from a single dictionary, conforming to standard size and checksum constraints.
> Electrum v1 does not implement checksum constraints.

> A mnemonic may be [referred to](https://wiki.trezor.io/Developers_guide:Cryptography) as `recovery seed` by some implementations. 
### Encoding
An `encoding` is a standard transform between mnemonics and a numeric representation.
### Entropy
The numeric representation of a mnemonic is its `entropy`.
> Both a mnemonic and its entropy represent the same [entropic](https://en.wikipedia.org/wiki/Entropy) value.
### Passphrase
A `passphrase` is arbitrary text that may be combined with a mnemonic in the formation of a seed.
> Electrum v1 does not implement a passphrase.
### Seed
A `seed` is a [one-way hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) of a mnemonic or its entropy.
### Master Private Key
A `master private key` is a secret number that defines a Bitcoin wallet, allowing spending.
> This is either the seed or [one-way](https://en.wikipedia.org/wiki/One-way_function) derived from it, depending on implementation.

> Electrum and typical BIP39 wallets encode this in accordance with [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#serialization-format).

> Electrum v1 represents this as a base16-encoded 32 byte number (elliptic curve private key).
### Master Public Key
A `master public key` is a non-secret number, allowing receiving.
> This is one-way derived from the master private key.

> Electrum and typical BIP39 wallets encode this in accordance with [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#serialization-format).

> Electrum v1 represents this as base16-encoded 64 byte number (uncompressed elliptic curve public key without a sign prefix).
## Hazards
### Unicode!
### String Functions
### Language Differences

## Dictionaries
### Normalization
### Intersection
### Sort/Search

## Encoding
### Language (words of a single language)
### Languages (sentences of any language)
### Dictionary (search a language)
### Dictionaries (search a set of dictionaries)
### Mnemonics (sentences and dictionaries)
### Base2048 (Electrum and BIP39)
### Base1626 (Electrum v1)

## Electrum v1
### Dictionaries
### Word Dependence
### Word Normalization
### Unexpected Behavior
#### Entropy Overflow
#### String Data
### Encoder
### Decoder
### Stretcher
### Keys

## Electrum
### Dictionaries
### Word Independence
### Word Normalization
#### Unicode Split
#### Unicode NFKD
#### Unicode Lower
#### Unicode Combining
#### Not Diacritics!
#### CJK Compression
#### ASCII Join
#### Ideographic Space
### Passphrase Normalization
### Checksum (prefix/version)
### Entropy Independence
### Entropy Loss
### Encoder
### Decoder
### Grinder
### Seeder
### Keys

## BIP39
### Dictionaries
### Word Dependence
### Word Normalization
#### Unicode Split
#### Unicode NFKD
#### Unicode Lower
#### ASCII Join
#### Ideographic Space
### Passphrase Normalization
### Checksum
### Entropy Dependence
### Encoder
### Decoder
### Seeder
### Keys
