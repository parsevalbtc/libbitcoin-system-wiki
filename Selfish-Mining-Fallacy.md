The term [selfish](Glossary#selfish) [mining](Glossary#mine) refers to a mining *optimization*. However, one [academic paper](https://www.cs.cornell.edu/~ie53/publications/btcProcFC.pdf) frames the optimization as follows:

> Conventional wisdom asserts that the mining protocol is incentive-compatible and secure against colluding minority groups, that is, it incentivizes miners to follow the protocol as prescribed. We show that the Bitcoin mining protocol is not incentive-compatible.
>
> *Ittay Eyal and Emin Gün Sirer: Majority is not Enough*

This statement assumes a "prescribed Bitcoin mining protocol" that precludes [withholding](Glossary#withholding), which is a [straw man](https://en.wikipedia.org/wiki/Straw_man). Bitcoin [consensus rules](Glossary#consensus-rules) are necessarily silent on the timing of [announcements](Glossary#announcement).

> We present an attack with which colluding miners obtain a revenue larger than their fair share.

This statement assumes a concept of "fair share" that is foreign to Bitcoin, another straw man. A [miner](Glossary#miner) is [rewarded](Glossary#reward) based on [blocks](Glossary#block) that reach [maturity](Glossary#maturity), not as a proportion of actual [hash rate](Glossary#hash-rate).

These straw men are explicitly attributed to "conventional wisdom". In other words the paper uses them to show that the conventional wisdom is incorrect. However, the paper errs in unconditionally declaring that this *unfair violation of the protocol* constitutes an attack:

> This attack can have significant consequences for Bitcoin: Rational miners will prefer to join the selfish miners, and the colluding group will increase in size until it becomes a majority. At this point, the Bitcoin system ceases to be a decentralized currency.

This is the source of the fallacy. It is not an attack for conventional wisdom to be incorrect, it is an error in the presumed conventional wisdom. Selfish mining implies that Bitcoin exhibits [latency](Glossary#latency)-based [pooling](Glossary#pooling) pressure, though this is a [well-established flaw](Proximity-Premium-Flaw). All pooling pressures tend to reduce the number of miners, exposing Bitcoin to attacks.

**Optimizations are not attacks.** Pooling increases the *opportunity* for attacks, but opportunity should not be conflated with action. The term "[attack](Glossary#attack)" implies theft. In fact, the Bitcoin [whitepaper](https://bitcoin.org/bitcoin.pdf), uses the term only to describe [double-spend](Glossary#double-spend) attempts.
