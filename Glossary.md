## Fundamentals
#### Person
A decision maker.
#### Machine
An instruction follower.

## Agreement
#### Bitcoin
The set of principles that secure a [Coin](#coin) from the [State](#state).
> The term and principles are defined by Satoshi in “Bitcoin: A Peer-to-Peer Electronic Cash System”.
#### Consensus
An agreement among [People](#person).
> Also the set of people who participate in an agreement.
#### Coin
A [Consensus](#consensus) regarding a mutually-acceptable medium for [Trade](#trade).
> BTC is a Coin.
#### Consensus Rules
The set of constraints that define a [Coin](#coin).
#### Rule
A subset of [Consensus Rules](#consensus-rules).
#### Validity
Conformance to [Consensus Rules](#consensus-rules).
#### Validation
The process of determining [Validity](#validity).
#### Enforcement
The act of discarding [Invalid](#validity) data.

## Objects
#### Unit
A minimum transferable amount of property represented by a [Coin](#coin).
> The Satoshi is the Bitcoin unit.
#### Transfer
The change of control over certain [Units](#unit).
#### Transaction
A [Valid](#validity) record of [Transfer](#transfer).
#### Block
A [Valid](#validity) set of [Transactions](#transaction) with [Timestamp](#timestamp) and [Proof](#proof).
#### Chain
The [Branch](#branch) with the most cumulative [Proof](#proof).

## Transactions
#### Script
A set of [Operations](#operation) that authorizes [Transfer](#transfer).
#### Operation
An atomic declaration of intent.
#### Contract
A [Script](#script) that expresses [Transfer](#transfer) conditions.
> Public Key Script is an anachronism for this.
#### Endorsement
A [Script](#script) that satisfies a [Contract](#contract).
> Signature Script is an anachronism for this.
#### Point
A reference to an [Output](#output) or [Input](#input).
#### Output
An explicit [Transfer](#transfer) and a [Contract](#contract).
#### Input
An [Output](#output) [Point](#point) and an [Endorsement](#endorsement).
#### Previous Output
The [Output](#output) to which an [Input](#input) refers.
#### Locktime
An expression of earliest [Transaction](#transaction) [Validity](#validity).
#### Dust
An insufficient number of [Units](#unit) for [Transfer](#transfer) via an [Output](#output).
> BTC consensus rules prohibit transfer of less than one unit.

## Blocks
#### Timestamp
A declaration of the time of [Block](#block) production.
#### Median Time Past
An average of preceding [Block](#block) [Timestamps](#timestamp).
#### Proof
Probabilistic evidence of the amount of [Work](#work) performed.
#### Branch
A [Valid](#validity) sequence of [Blocks](#block).
#### Weak
A [Branch](#branch) with less cumulative [Proof](#proof) than another.
> Orphan is a misnomer for this.
#### Strong
A [Branch](#branch) with more cumulative [Proof](#proof) than another.

## Sequence
#### Confirmation
Inclusion of a [Transaction](#transaction) in a [Block](#block).
#### Unconfirmed
A [Transaction](#transaction) that does not exist in a [Block](#block).
#### Transaction Pool
The set of [Unconfirmed Transactions](#unconfirmed).
> Memory Pool is a misnomer for this.
#### Block Pool
The set of [Weak Blocks](#weak).
> Orphan Pool is a misnomer for this.
#### Genesis
The first [Block](#block) of all [Branches](#branch) of a [Coin](#coin).
#### Depth
One more than the count of [Blocks](#block) after a [Confirmation](#confirmation).
#### Height
The count of preceding [Blocks](#block) in a [Branch](#branch).
#### Segment
A contiguous subset of a [Branch](#branch).
#### Organization
An [Announcement](#announcement) adding a [Block](#block) to the [Chain](#chain).
#### Period
The average time between [Organizations](#organization).
#### Layering
[Trade](#trade) using a sequence of [Unconfirmed](#unconfirmed) [Transactions](#transaction) that can be [Settled](#settlement) by either party.
#### Settlement
[Confirmation](#confirmation) of [layered](#layering) [transactions](#transaction).

## Money
#### Spend
The initial publication of a [Transaction](#transaction).
#### Double Spend
The [Endorsement](#endorsement) of the same [Output](#output) [Contract](#contract) by distinct [Spends](#spend).
#### Subsidy
The issuance of new [Units](#unit) to a [Miner](#miner).
#### Inflation
The increase in [Supply](#supply) resulting from [Subsidy](#subsidy).
> Also monetary inflation, not to be confused with price inflation.
#### Fee
An implicit [Transfer](#transfer) to a [Miner](#miner).
#### Reward
The sum of [Subsidy](#subsidy) and [Fees](#fee) for a [Block](#block).
#### Coinbase
A [Transaction](#transaction) that [Transfers](#transfer) a [Reward](#reward).
#### Maturity
The [Depth](#depth) at which a [Coinbase](#coinbase) [Output](#output) becomes [Transferable](#transfer).
#### Halving
A reduction in the [Subsidy](#subsidy) rate (by half).
#### Difficulty
The level of [Proof](#proof) required for [Validity](#validity).
#### Adjustment
A change to [Difficulty](#difficulty).
#### Cap
The limit to [Supply](#supply) over all time.
#### Price
A moving average of [Exchange](#exchange) rates.
#### Capitalization
The product of [Price](#price) and [Supply](#supply).

## Economics
#### Trade
A voluntary swap of property between two [People](#person).
#### Utility
The usefulness of certain property to a [Person](#person).
#### Value
The preference of a [Person](#person) for certain property over other.
#### Supply
The set of all issued [Units](#unit).
#### Exchange
The [Trade](#trade) of [Units](#unit) for other property.
#### Price Inflation
The increase in average [Exchange](#exchange) prices over time.
#### Hoard
To [Own](#owner) for future use.
> This is neither speculation nor investment.
#### Speculate
To [Own](#owner) in expectation of [Price](#price) increase.
> Also to borrow in expectation of price decrease.
#### Lend
To [Trade](#trade) time without [Units](#units) for property of greater [Utility](#utility).
> Invest is an alias for this.
#### Borrow
To [Trade](#trade) time with [Units](#units) for property of greater [Utility](#utility) to the [Lender](#lend).
#### Interest
The rate of increase in [Utility](#utility) from [Lending](#lend).
#### Profit
The return on [Speculation](#speculate).
> This excludes interest.
#### Loss
Failure of [Investment](#lend) to earn [Interest](#interest).
> This is negative profit.
#### Volatility
Deviation in [Price](#price) over time.
#### Market
The [Trade](#trade) in certain property.

## Network
#### Communication
Conveyance of data between [Machines](#machine).
#### Protocol
A set of [Communication](#communication) conventions.
#### Peer-to-Peer
A symmetrical [Protocol](#protocol).
#### Client-Server
An asymmetrical [Protocol](#protocol).
#### Latency
The delay inherent in [Communication](#communication).
#### Partition
An inability of certain [Nodes](#node) to [Communicate](#communication).
#### Denial of Service
Using [Communication](#communication) to exploit [Protocol](#protocol) or [Implementation](#implementation) flaws that  degrade performance.
> DoS is an acronym for this.

## Components
#### Mine
A [Tool](#tool) that performs [Work](#work).
#### Grind
A [Tool](#tool) that performs [Hashing](#hash).
#### Relay
A [Tool](#tool) that disseminates new [Blocks](#block).
#### Node
A [Tool](#tool) that performs [Validation](#validation).
#### Wallet
A [Tool](#tool) that creates [Transactions](#transaction).
#### Tool
A set of [Machine](#machine) instructions.
#### Implementation
A specific [Tool](#tool) set.

## Actors
#### Miner
A [Person](#person) operating a [Mine](#mine).
#### Grinder
A [Person](#person) operating a [Grind](#grind).
#### Relayer
A [Person](#person) operating a [Relay](#relay).
#### Merchant
A [Person](#person) accepting [Units](#unit) in [Trade](#trade).
> User is a common alias for this.
#### Owner
A [Person](#person) controlling certain [Units](#unit).
> Holder is a common alias for this.
#### Developer
A [Person](#person) creating an [Implementation](#implementation).
#### Claimant
A [Person](#person) who holds a claim on property under the control of a [Custodian](#custodian).
> Also a lien-holder, shareholder, lender, or depositor.
#### Custodian
A [Person](#person) who controls the property of another

## Mining
#### Work
The process of [Block](#block) production.
#### Candidate
A potential [Block](#block) with undetermined [Proof](#proof).
#### Hash
An atomic computation to [Prove](#proof) [Candidate](#candidate) [Validity](#validity).
#### Hash Rate
The rate of [Hashing](#hash).
#### Apparent Hash Power
A fraction of [Blocks](#block) in a [Chain](#chain) [Segment](#segment).
> Public estimates of miner hash power are based on this.
#### Majority Hash Power
A subset of [Miners](#miner) with sufficient [Hash Power](#hash-power) to execute a sustained [Attack](#attack).
> 51% is a common approximation of sufficient power.
#### Optimization
A [Tool](#tool) change that reduces the cost of [Mining](#mine).
#### Announcement
The first communication of a [Block](#block) to another [Person](#person).
#### Withholding
The purposeful delay of [Announcement](#announcement).
#### Honest
A [Miner](#miner) who builds on the [Blocks](#block) of others.
#### Selfish
A [Miner](#miner) who is not always [Honest](#honest).
#### Variance
The varying frequency of achieving a [Reward](#reward).
#### Decouple
A [Mine](#mine) that shares [Reward](#reward) with another to reduce [Variance](#variance).

## Deviations
#### Fork
A divergence in [Consensus Rules](#consensus-rules).
#### Hard Fork
A [Fork](#fork) that implies a [Split](#split).
> Expansion of the set of potentially-valid blocks.
#### Soft Fork
A [Fork](#fork) that implies a [Split](#split) unless [Enforced](#enforcement) by [Majority Hash Power](#majority-hash-power).
> Contraction of the set of potentially-valid blocks.
#### Split
A [Coin](#coin) bifurcation.
#### Reorganization
An [Announcement](#announcement) promoting a [Weak Branch](#weak) to the [Chain](#chain).
> Reorg is an abbreviation for this.
#### Stall
The lack of [Height](#height) increase over time.
#### Activation
Starting to [Enforce](#enforcement) a new [Rule](#rule).
#### Signal
A [Miner](#miner) indication via [Block](#block) data of intent to [Enforce](#enforcement) a new [Rule](#rule).

## Privacy
#### Identity
The means to associate [Communication](#communication) with a [Person](#person).
#### Taint
Determination of [Ownership](#owner).

## Security
#### Power
The relative level of control of a [Person](#person) over the [Chain](#chain) or [Coin](#coin).
#### Economy
The set of all [Merchants](#merchant).
#### Economic Power
A fraction of all property offered in [Exchange](#exchange).
#### Hash Power
A fraction of the [Hash Rate](#hash-rate) of all [Mines](#mine).
#### Attack
Use of [Hash Power](#hash-power) to enable [Double Spending](#double-spend).
> Confirmation prevention is a case of double-spend enabling.
#### Co-option
Use of aggression to control [Hash Power](#hash-power).
#### Coercion
Use of aggression to compel [Activation](#activation).
#### Distortion
[Market](#market) aggression that skews the cost of [Mining](#mine).
#### Variation
Differences in the resource cost of [Mining](#mine).
#### Censorship
Subjective [Confirmation](#confirmation).
#### State
A set of [People](#person) that uses aggression in place of [Trade](#trade).
> Typically operates with impunity within geographic limits.
#### Political
Pertaining to the actions of [States](#state).

## Weakness
#### Aggregation
The tendency toward reduced participation in [Mining](#mine) or [Validation](#validation).
> Implies pooling or centralization.
#### Pooling
The tendency toward few [Miners](#miner), including consolidation by [Relays](#relay).
> Collusion is a common alias for this.
#### Centralization
The tendency toward few [Merchants](#merchant).
> Merchants directly control validation.
#### Delegation
The tendency toward few [Owners](#owner).
> Owners directly control spending.
#### Partitioning
The tendency toward persistent [Partitions](#partition).
> Identity implies exclusion.
#### Correlation
The ability to [Taint](#taint) using statistical [Chain](#chain) analysis.