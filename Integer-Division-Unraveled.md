Sometimes the most trivial coding tasks can be unexpectedly challenging. In working with Python equivalence I found it necessary to implement floored signed integer modulo in C++.

A quick stop at Stack Overflow shows how confounding this can be. There are several different approaches, most of which either do not support signed operators, cause overflows, throw exceptions, or just don't work. Even the "accepted" answers are flawed in such ways, with large numbers of upvotes. So I decided to just figure it out myself.

## Objectives

* Provide ceilinged `\` and `%` functions, for all integer types.
* Maintain the failure behavior of native operators ( `x / 0` and `x % 0`)
* Maintain overflow behavior of native operators.
* Avoid all unnecessary computation.

## Analysis

The key is in understanding the nature of the native operators. Division is actually a challenging subject. Programming languages implement it in [different ways](https://en.wikipedia.org/wiki/Modulo_operation). Python actually [changed the behavior](https://www.python.org/dev/peps/pep-0238/) of `\` and `%` in a minor version release! This of course led to additional scrutiny in the original Python equivalence task.

> We propose to fix this by introducing different operators for different operations: x/y to return a reasonable approximation of the mathematical result of the division ("true division"), x//y to return the floor ("floor division"). We call the current, mixed meaning of x/y "classic division".

C++ implements `truncated` integer division. Python implements `floored` integer division. The other common form (`ceilinged`) is quite useful as well. Common hacks for ceilinged division are:

```cpp
// This overflows and is limited to signed integers.
auto q = (x + (y - 1)) / y;

// This is limited to signed integers.
auto q = (x / y) + !!(x % y);
```

There is no distinction unless there is a remainder - all rounding methods return the same result. But in integer division the remainder must be discarded. So the question becomes how to consistently round the quotient. All modulo operations will necessarily be consistent with this choice of quotient rounding, which means that they vary as well.

Truncated division simply drops the remainder. So in the case of a positive quotient, the quotient is floored (less positive) and in the case of a negative quotient, the quotient is ceilinged (less negative). This is also called "toward zero" rounding. In changing rounding behavior, one must know whether there is a remainder (and its magnitude when implementing `%`) and the sign of the quotient. The quotient is positive if both operands have the same sign, otherwise it is negative.

## Implementation

These two templates answer the above two questions.

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
```
These two templates combine them into single answer.
```cpp

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
```
These six templates implement the three common rounding approaches.
```cpp
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
```
```cpp
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
```
```cpp
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
The only thing that may not be obvious is that the `floored_modulo` function negates the dividend of the negative remainder in order to obtain its magnitude. These functions cannot *cause* overflows and *do* fail as native operators with a zero-valued divisor.

## Template Type Constraints and Specialization
The above (simplified) templates will result in compiler warnings for unsigned operators, as they all invoke `factor < 0`, which is always `false`. The full implementation type-constrains each template and specializes the `negative(...)` calls for operator unsigned types.

The above `floored_modulo` and `floored_divide` templates can be optimized by specializing for unsigned operators as these reduce to the truncated (native) forms.

## Optimization
The `%` operator may be invoked twice in `floored_modulo` (with signed operators) and `ceilinged_modulo`. The first tests for remainder and the second produces it. It does not seem worth denormalizing the implementation by adding a variable to cache the value in the (sometimes) case where there is a non-zero remainder. This would also require dividend negation in `floored_modulo` where it may not be necessary (i.e. zero remainder), probably a break-even. So in these cases I am relying on CPU cache and compiler optimization to avoid remainder recomputation (which should be the case).

The `inline` keyword is used to advise the compiler that we would prefer inlining of the functions. This removes call stack overhead, assuming the compiler respects the request. Generally I prefer to let the compiler make these decisions, preserving code readability.

The compiler is expected to reduce the redundant `truncated_divide` calls in `ceilinged_divide` and `floored_divide` and `truncated_modulo` calls in `ceilinged_modulo`. However doing it explicitly is at best an object code *size* optimization, not a performance optimization.

Despite the verbosity of the templates, their type constraints, function and variable names, the implementation should be as optimal as manually inlining the minimal *necessary* operations.

## Conclusion
* The `remainder(...)` call compiles away for `constexpr` operators.
* The specialized `negative(...)` calls compile away for `unsigned` types.
* The `||` conditions compile away when the above render the result always `true` or `false`.

This implies that what remains is *necessary*.