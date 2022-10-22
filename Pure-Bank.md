The concept of a Pure Bank can be useful in demonstrating [lending](Glossary#lend) behavior generally.

A pure bank provides only the following services:

* borrows money (debt from creditors)
* lends money (credit from debtors)
* hoards money (reserve)

The material differences from a real bank are:

* no state intervention (free bank)
* no cost of operation (perfectly efficient)

The bank is owned by its creditors in proportion to their credit, as is the case with any company. There are existing major banks that are owned by their account-holders, such as [USAA](https://www.usaa.com) and [Vanguard](https://investor.vanguard.com), so this is not a distinction from a real bank. Neither a pure bank nor a real bank has "own capital" to lend, as all capital is borrowed from investors in one form or another. The objective of creditors is to maximize their rate of return. The objective of debtors is to minimize their [interest](Glossary#interest) expense.

Creditor accounts are [money substitutes](https://wiki.mises.org/wiki/Money_substitutes). This aspect distinguishes the bank from an investment fund. The money substitute may be either a [demand deposit](https://en.wikipedia.org/wiki/Demand_deposit) or a [money market](https://en.wikipedia.org/wiki/Money_market_fund). The distinction is in the allocation of insufficient reserve (negative rate of return), with the former being "[first come, first served](https://en.wikipedia.org/wiki/Bank_run)" and the latter "[breaking the buck](https://en.wikipedia.org/wiki/Money_market_fund#Breaking_the_buck)".

The lack of state intervention is the common concept of [free banking](https://en.wikipedia.org/wiki/Free_banking), where there is no [statutory control](https://en.wikipedia.org/wiki/Federal_Reserve), [state insurance](https://www.fdic.gov), [discount capital](https://en.wikipedia.org/wiki/Discount_window), or [seigniorage](https://en.wikipedia.org/wiki/Seigniorage). The bank uses [commodity money](Money-Taxonomy) unless otherwise specified, which simplifies calculations by [eliminating](Inflation-Principle) the need to offset [price inflation](https://en.wikipedia.org/wiki/Inflation) or [price deflation](https://en.wikipedia.org/wiki/Deflation).

Perfect efficiency differs from a real bank only in the rate of return, as nothing is consumed in operations. All earning is a consequence of [time preference](Time-Preference-Fallacy). Uniform interest is assumed, as rate [arbitrage](https://en.m.wikipedia.org/wiki/Arbitrage) is an expense. [Demurrage](https://en.wikipedia.org/wiki/Demurrage_(currency)) is the expense of storing money. The expense ratio (inclusive of demurrage) is 1 for the Pure Bank.

[Reserved](Reserve-Definition) capital is the money in which credit and debt are [settled](https://en.wikipedia.org/wiki/Settlement_(finance)) (zero [maturity](https://en.wikipedia.org/wiki/Maturity_(finance))). [Depreciation](Depreciation-Principle) is the [opportunity cost](https://en.wikipedia.org/wiki/Opportunity_cost) of it not being loaned, also known as "cash drag". Interest relations assume a single [compounding period](https://en.wikipedia.org/wiki/Compound_interest) with the rate of interest over that period. This presentation simplification is inconsequential to implied relations.

Given the preceding definition of a pure bank, the following relations are absolute.
```
reserved     = borrowed - loaned
demurrage    = demurrage-rate * reserved
depreciation = interest-rate * reserved
interest     = interest-rate * loaned
return       = expense-ratio * interest
```
For the pure bank, the [reserve ratio](https://en.wikipedia.org/wiki/Reserve_requirement) fully determines [capital ratio](https://en.wikipedia.org/wiki/Capital_requirement), [debt ratio](https://en.wikipedia.org/wiki/Debt_ratio), and savings ratio.
#### Reserve Ratio
```
reserve-ratio = reserved / borrowed
reserve-ratio = (borrowed - loaned) / borrowed
```
#### Capital Ratio
```
capital-ratio = reserved / loaned
capital-ratio = (borrowed - loaned) / loaned
```
#### Debt Ratio
```
debt-ratio = borrowed / reserved
debt-ratio = borrowed / (borrowed - loaned)
```
#### Savings Ratio
```
savings-ratio = loaned / reserved
savings-ratio = loaned / (borrowed - loaned)
```
#### Balance Sheet
The pure bank has no liabilities, only shareholder equity.

|bank assets     |shareholder equity |
|----------------|-------------------|
|lent + reserved |borrowed           |

#### Rate of Return
Creditor rate of return is additionally a function of the interest rate. The creditor's rate of return is less than the debtor's interest rate due to cash drag, the necessary expense of demand withdrawal. To reduce this expense, time constraints are typically included in [real bank contracts](https://www.chase.com/content/dam/chasecom/en/checking/documents/deposit_account_agreement.pdf). For example, by law any withdrawal from an interest-bearing U.S. bank account can be delayed for seven days. The creditor can only eliminate cash drag by holding the debt in an investment fund (i.e without settlement assurances).
```
return-rate = interest-rate * loaned / borrowed
```
As shown in [Savings Relation](Savings-Relation) individual capital ratios fully determine the market interest rate. When we consider every person operating as a pure bank, it becomes clear that the capital ratio determines the interest rate. A capital ratio of 0% for all people implies that capital is free and has no return. At increasing capital ratios, the interest rate increases accordingly. At full hoarding the cost of capital is “infinite” – none can be obtained for production.

The presumption of the [money relation](Inflation-Principle) is that price is proportional to the ratio of demand to supply. But as shown in Savings Relation, supply and demand for capital exist in a zero-sum relation. An increase in [hoarding](Glossary#hoard) implies a corresponding decrease in lending and the reverse implies an increase. As such neither the capital ratio nor the interest rate is linear in relation to change in the amount hoarded (or lent). This has led some to search for a “[golden ratio](https://en.wikipedia.org/wiki/Golden_Rule_savings_rate)”. Yet given the subjectivity of value, this is ultimately an exercise in futility.

Yet capital ratios fully determine the interest rate. As all people individually attempt to obtain a golden ratio based on their own preferences, the market rate of interest results. Substituting the capital ratio for the interest rate demonstrates the effect of reservation on the Pure Bank, under the additional assumption that everyone operates as a Pure Bank and with the same capital ratio. The capital ratio includes present goods depreciation, which for money is demurrage. The Pure Bank demurrage ratio is 1, so this drops out.
```
return-rate = (reserved * demurrage-ratio / loaned) * (loaned / borrowed)
return-rate = (reserved / borrowed) * demurrage-ratio
return-rate = reserved / borrowed
```
The rate of return on Pure Bank investment becomes the reserve ratio. This does not
imply that an individual Pure Bank can set its own return by setting its capital ratio. It merely reflects that the market capital ratio determines the return on capital. If *all lenders* doubled their present capital ratio their returns would necessarily double, as the cost for capital, and therefore its return, would double.

#### Real Banks
The independent capital ratios of all people, based on individual time preference, determine the market rate of interest. The above substitution for the bank's own capital ratio as the interest rate seems to imply that the bank is setting the interest rate. However this is inherent in the concept of time preference. A bank can set any level of interest it prefers. There is no assumption for real banks that the market will oblige, so market interest and therefore market returns are assumed.
```
market-return-rate = market-interest-rate * (loaned / borrowed)
market-return-rate = market-capital-ratio * (loaned / borrowed)
```
The free bank also differs from the pure bank by operational expense, which directly reduces rate of return.
```
free-bank-return-rate = market-return-rate * expense-ratio
```
The Real Bank only differs from the free bank by tax (inclusive of regulatory expense), which directly reduces rate of return.
```
real-return-rate = free-bank-return-rate * tax-expense-ratio
```
The Central Bank (state) only differs from the real bank by taxpayer subsidy (inclusive of discounted borrowing), which directly increases rate of return.
```
central-return-rate = real-bank-return-rate * subsidy-income-ratio
```
Where tax includes seigniorage of the bank money, the [Fisher Equation](https://en.wikipedia.org/wiki/Fisher_equation) must be applied above to translate the interest rate from a nominal rate to a real rate. No other change is implied other than tax, which is accounted for by the Real Bank above. This tax is generally the source of subsidy, which is accounted for by the Central Bank above.

Every [person](Glossary#person), or company of people, is a Real Bank, and the [state](Glossary#state) is a Central Bank. A Real Bank produces the service of liquid investment, an [economic good](https://en.wikipedia.org/wiki/Goods). The cost of production is the depreciation of its reserve. This is the model of all production.
