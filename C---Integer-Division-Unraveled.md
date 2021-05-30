```cpp
template <typename Dividend, typename Divisor>
inline bool remainder(const Dividend dividend, const Divisor divisor)
{
    return (dividend % divisor) != 0;
}

template <typename Factor1, typename Factor2>
inline bool negative(const Factor1 factor1, const Factor2 factor2)
{
    return (factor1 <0) != (factor2 < 0);
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