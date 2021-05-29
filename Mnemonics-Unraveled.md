## Background
Libbitcoin support for mnemonic wallet seed encoding began with [Electrum](https://electrum.org/) v1. Later came [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki), driven by the fine folks behind [Trezor](https://wiki.trezor.io/Developers_guide:Cryptography), which we believed Electrum was adopting. When the fine folks behind Electrum [decided against BIP39](https://electrum.readthedocs.io/en/latest/seedphrase.html#motivation), we found ourselves with three implementations. We had dropped Electrum v1 in the expectation that BIP39 would become sufficient. Later we added Electrum but found it necessary to also restore Electrum v1. It is not possible to properly implement Electrum mnemonic support without also implementing Electrum v1 and BIP39 mnemonics.

An overhaul of our mnemonic implementations was well overdue. What was anticipated to require one week required over a month of full time work. Test coverage is nearly complete and it will be merged soon. Before I forget the various lessons learned, I decided to write them down here. The information is all out there, somewhere. But ultimately it required digging through a lot of Python and C code. Wallet seeds are not something for a developer to take lightly, and code is always authoritative. Eventually I found myself sifting through Python internals, a deeper rabbit hole than I expected.

I will state for the record that I truly appreciate both Electrum and Trezor. Otherwise I would not have spent the time to provide comprehensive support for all three of these encodings. These observations are provided for my own record and to possibly aid others who may at some point find themselves in that same rabbit hole. When one goes this deep into implementation, interesting discoveries abound.

## Terminology
### Dictionary
### Word
### Mnemonic
### Encoding
### Entropy
### Passphrase
### Seed
### Key

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
### Prefix/Version
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
