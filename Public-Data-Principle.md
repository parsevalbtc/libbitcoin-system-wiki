It follows from the [Risk Sharing Principle](Risk-Sharing-Principle) that system security depends upon covert [mining](Glossary#mine) and [trade](Glossary#trade). A [coin](Glossary#coin) exists as a [mutually-beneficial](Balance-of-Power-Fallacy) [market](Glossary#market) between [miners](Glossary#miner) and [merchants](Glossary#merchant) for the [confirmation](Glossary#confirmation) of [transactions](Glossary#transaction) within [blocks](Glossary#block) in exchange for [fees](Glossary#fee).

The necessarily covert activities are listed by role:

**Miner**
1. obtain blocks [to build upon]
2. obtain unconfirmed transactions [to earn fees from]
3. create and distribute blocks [to cause others to build upon]
4. receive payment for confirmations [to finance operations]

**Merchant**
1. obtain blocks [to validate customer payment]
2. obtain unconfirmed transactions (optional) [to anticipate payments and fees]
3. create and distribute transactions [to obtain customer payment]
4. make payment for confirmations [to compensate confirmation]

If blocks cannot be obtained anonymously the system is insecure. The inability to obtain the [strongest](Glossary#strong) blocks available to other [people](Glossary#person) is a network [partition](Glossary#partition), which implies localized insecurity. However neither anonymity, nor its opposite [identity](Glossary#identity), can ensure one sees the strongest [branch](Glossary#branch) at any given time. In other words, any attempt to mitigate partitioning with the introduction of identity is a [false choice](https://en.wikipedia.org/wiki/False_dilemma) that sacrifices system security for the false promise of ensuring localized security.

It is not essential that all miners or merchants see all transactions at any given time. However broad visibility is preferable as it produces the most robust competition for fees and best leading information. In other words, a market where every participant sees all of the transactions all of the time is a [perfect market](https://en.wikipedia.org/wiki/Perfect_competition). Asking the network for specific transactions, as opposed to all (or summary information about all), is a source of taint and must be avoided in the interest of security as well.

Creation of blocks and transactions does not inherently expose identity, however public distribution of either is the primary source of [taint](Glossary#taint). To the extent that miners openly self-identify, they are relying on the assumption of a [low-threat environment](Threat-Level-Paradox), not contributing to system security. Avoiding taint when disseminating blocks and transactions requires use of an [anonymous connection](https://en.wikipedia.org/wiki/Anonymizer) to a community [server](Glossary#client-server). This ensures the [distribution network](Glossary#peer-to-peer) never has access to identifying information.

[Proof-of-work](Glossary#proof-of-work) preserves the anonymity of miners. There is no signature associated with mining and energy is presumed to be ubiquitous. Similarly, the ability to pay anonymously for confirmation is the reason for transaction fee inclusion. It is [sufficient](Side-Fee-Fallacy) to pay a miner directly (off [chain](Glossary#chain)) for confirmation, however this exposes the merchant and miner to each other, and makes it more difficult to estimate fees anonymously.

Bitcoin is novel in that all financial transactions can be [validated](Glossary#validation) from public data and without identity. Centralized financial systems rely on either trust in (cryptographically-identifiable) connections to other parties or trust in (cryptographically-verifiable) signatures on transmitted data. This is the essence of trust-based systems; certain authorities have secrets that others use to verify that authenticity. **The reason for validation is to eliminate the use of identity and thereby authority.**
