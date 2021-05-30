Sometimes the most trivial coding tasks can be unexpectedly challenging. In working with Python equivalence I found it necessary to implement floored integer division and modulo functions in C++.

A quick stop at Stack Overflow shows how confounding this can be. There are several different approaches, most of which either do not support signed operators, cause overflows, throw exceptions, or just don't work. The "accepted" answers are flawed in such ways, with large numbers of upvotes.

So I decided to just figure it out myself. The objectives are:

* Provide ceilinged `\` and `%` functions, for all integer types.
* Maintain the failure behavior of native operators ( `x / 0` and `x % 0`)
* Maintain overflow behavior of native operators.
* Avoid all unnecessary computation.

The key to achieving this is to understand the nature of the native C++ operators. Division is actually a challenging subject. Programming languages implement it in [different ways](https://en.wikipedia.org/wiki/Modulo_operation). Python actually [changed the behavior](https://www.python.org/dev/peps/pep-0238/) of `\` and `%` in version 2.1 (which of course led to additional challenges in the original Python equivalence task).

C++ implements "truncated" division. The other common forms are "floored" and ceilinged". There is no distinction unless there is a remainder. But in integer division the remainder must be discarded. So the question becomes how to consistently round the quotient. All modulo operations will necessarily be consistent with this choice of quotient rounding, which means that they vary as well.

```cpp
template <typename Dividend, typename Divisor>
inline bool remainder(const Dividend dividend, const Divisor divisor)
{
    return (dividend % divisor) != 0;
}

template <typename Factor1, typename Factor2>
inline bool negative(const Factor1 factor1, const Factor2 factor2)
{
    return (factor1 < 0) != (factor2 < 0);
}

template <typename Dividend, typename Divisor>
inline Dividend ceilinged(const Dividend dividend, const Divisor divisor)
{
    return !remainder(dividend, divisor) || negative(dividend, divisor);
}

template <typename Dividend, typename Divisor>
inline Dividend floored(const Dividend dividend, const Divisor divisor)
{
    return !remainder(dividend, divisor) || !negative(dividend, divisor);
}

template <typename Dividend, typename Divisor>
inline Dividend ceilinged_modulo(const Dividend dividend, const Divisor divisor)
{
    return ceilinged(dividend, divisor) ?
        truncated_modulo(dividend, divisor) :
        divisor + truncated_modulo(dividend, divisor);
}

template <typename Dividend, typename Divisor>
inline Dividend ceilinged_divide(const Dividend dividend, const Divisor divisor)
{
    return ceilinged(dividend, divisor) ?
        truncated_divide(dividend, divisor) :
        truncated_divide(dividend, divisor) + 1;
}

template <typename Dividend, typename Divisor>
inline Dividend floored_modulo(const Dividend dividend, const Divisor divisor)
{
    return floored(dividend, divisor) ?
        truncated_modulo(dividend, divisor) :
        divisor - truncated_modulo(-dividend, divisor);
}

template <typename Dividend, typename Divisor>
inline Dividend floored_divide(const Dividend dividend, const Divisor divisor)
{
    return floored(dividend, divisor) ?
        truncated_divide(dividend, divisor) :
        truncated_divide(dividend, divisor) - 1;
}

template <typename Dividend, typename Divisor>
inline Dividend truncated_modulo(const Dividend dividend, const Divisor divisor)
{
    return dividend % divisor;
}

template <typename Dividend, typename Divisor>
inline Dividend truncated_divide(const Dividend dividend, const Divisor divisor)
{
    return dividend / divisor;
}
```