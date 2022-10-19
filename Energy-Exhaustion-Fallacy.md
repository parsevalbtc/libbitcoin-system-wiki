There is a theory that [proof-of-work](Glossary#proof-of-work) (PoW) may exhaust all energy available to people. PoW converts energy into a [monotonically increasing](https://en.wikipedia.org/wiki/Monotonic_function) [double-spend](Glossary#double-spend) barrier for any given [transaction](Glossary#transaction). This is comparable to the energy expended in securing any money against counterfeit (by its own issuer or otherwise).

The purpose of any security measure is to create a cost necessary to overcome the measure, i.e. a financial barrier. Bitcoin creates its double-spend barrier by compelling the [attacker](Glossary#attack) to replace the [branch](Glossary#branch) of the targeted transaction with one of probabilistically greater [work](Glossary#work). Interestingly, such a replacement raises the barrier to subsequent attackers. **The energy expended is not independently important, the erected barrier is the attacker's necessary financial burden.**

The security barrier (S) of a [block](Glossary#block) is the product of unit [hash](Glossary#hash) cost (C), [hash rate](Glossary#hash-rate) (H), and period (T).
```
S = C * H * T
```
The [adjustment](Glossary#adjustment) varies [hash rate](Glossary#hash-rate) to maintain a constant period for a given hash cost and security.
```
T = S / (C * H)
```
A constant period implies that hash rate is inversely proportional to cost for a given security.
```
H ~ S / C
```
As energy supply is reduced its [price](Glossary#price) must increase, which reduces the amount expended for a given level of security. Therefore energy cannot be exhausted by [mining](Glossary#mine) and the theory is invalid.
