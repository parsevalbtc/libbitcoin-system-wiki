**Decentralization Model**

In [Social Network Principle](Social-Network-Principle) it is shown that Bitcoin is a network of [human](Glossary#person) relationships. This can be modeled as a [directed graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)#Directed_graph) where each vertex represents a [merchant](Glossary#merchant) and each edge represents a [trade](Glossary#trade) for bitcoin. Edges indicate the direction of movement of [coin](Glossary#coin) and are quantified in the number of [units](Glossary#unit) traded. All [owners](Glossary#owner) are presumed to have been merchants at the time of coin receipt, including as [miners](Glossary#miner) (selling [confirmations](Glossary#confirmation)) and as recipients of charity (selling [goodwill](https://en.wikipedia.org/wiki/Goodwill_(accounting))).

If a person is not personally accepting coin, or does not personally [validate](Glossary#validation) coin accepted, the person cannot reject invalid coin. The person is entrusting this task to a [central](Glossary#centralization) authority. **All people utilizing the same delegate are reduced to just one vertex that represents the delegate.**

For any period of time, [economic](Glossary#economy) security is a function of the number of merchants and the similarity of amounts traded. The strongest economy would be all people in the world trading for the same number of units in the period, an ideal which can be called a "distributed" (or fully-decentralized) economy. The weakest would be one delegate accepting all units traded in the period, which would be a "centralized" economy.

More specifically, the system is most economically decentralized which has the greatest number of vertices (merchants) with the lowest [coefficient of variation](https://en.wikipedia.org/wiki/Coefficient_of_variation) in the incoming edges (receipts). Defining a *distribution* function as the inverse of coefficient of variation we obtain:
```
economic-decentralization = distribution(receipts) * merchants
```

Similar to economic security, confirmation security can be modeled as an [edgeless graph](https://en.wikipedia.org/wiki/Null_graph). Each [miner](Glossary#miner) is represented by one vertex on the graph. A [grinder](Glossary#grinder) is not a miner as the grinder has no decision-making ability, only the miner is represented. The total [hash power](Glossary#hash-power) employed by a miner is the weight of the vertex.

For any period of time, confirmation security is a function of the number of miners and the similarity of hash power they directed. The strongest censorship resistance would be all people in the world mining at the same hash power in the period, an ideal which can be called "distributed" (or fully-decentralized) confirmation. The weakest would be one miner with 100% of hash power, which would be "centralized" confirmation.

More specifically, the system is most decentralized in confirmation which has the greatest number of vertices (miners) with the highest distribution in weights (hash power):
```
confirmation-decentralization = distribution(hash-power) * miners
```

**Security Model**

[Decentralization](Glossary#decentralization) alone is not security. Security is the product of activity, distribution of that activity, and the fraction of participating humanity.
```
security = activity * distribution * participation
```

Given that there is no limit to humanity, trade or computation, the level of security in each axis is unbounded. Security is also unbounded with perfect distribution (i.e. infinite decentralization). A minimum level of zero in each is achieved with either no participation or no activity. Economic and confirmation security can thus be defined as:
```
economic-security     = receipts   * distribution(receipts)   * [merchants / humanity]
confirmation-security = hash-power * distribution(hash-power) * [miners    / humanity]
```

**Limits of the Model**

These relations do not say anything about the absolute effectiveness represented by any value, or the relative effectiveness of any two values except that a greater value represents a greater effectiveness. This is not due to a deficiency in the model. The factors include people, specifically the effectiveness of their individual abilities to [resist](https://github.com/libbitcoin/libbitcoin/wiki/Axiom-of-Resistance) and their perception of [value](Glossary#value) in the money. All who validate or mine offer some level of resistance, but there is no implied continuity. We refer to a "level" of security, not an "amount" of security.

As show in in [Public Data Principle](Public-Data-Principle), anonymity is a tool that aids in defending one's ability to trade and/or mine. As such the level of decentralization can never be measured; the model is a conceptual aid. As shown in [Balance of Power Fallacy](Balance-of-Power-Fallacy), the security afforded by each of the two sub-models is complimentary and independent of the other. While people could decide to trade and/or mine independently in the future, the [Cockroach Fallacy](Cockroach-Fallacy) shows that they are not contributing to security until they do so. The model represents security as it exits in the period.
