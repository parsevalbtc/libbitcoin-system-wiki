[Logarithms](https://en.wikipedia.org/wiki/Logarithm) and powers ([exponents](https://en.wikipedia.org/wiki/Exponentiation)) are occasionally useful in Bitcoin work, especially base ([radix](https://en.wikipedia.org/wiki/Radix)) 2. But C++ [log functions](https://en.cppreference.com/w/cpp/numeric/math/log) are all [floating point](https://en.wikipedia.org/wiki/Floating-point_arithmetic). There is not much call for floating point calculation in Bitcoin, and using the wrong data types causes [code bloat](https://en.wikipedia.org/wiki/Code_bloat) due to [type casting](https://en.cppreference.com/w/cpp/language/explicit_cast), and compiler warnings otherwise. It can also result in logic errors due to unexpected [rounding](https://en.cppreference.com/w/cpp/numeric/math/round) behavior. Floating point operations may also be more CPU intensive than those necessary for [integer](https://en.wikipedia.org/wiki/Integer) operations. So we cleaned up some code by implementing a proper suite of integer exponentiation functions.

> Natural logarithms are inherently floating point (base *e*) and logarithms of negative numbers are non-continuous functions, so implementation of these is a non-objective.

## Objectives
* Provide power and log, for all integer types.
* Provide simplified base 2 variants of power and log.
  * log2(n) identical in behavior to log(2, n).
  * power2(n) identical in behavior to power(2, n).
* Provide both ceilinged and floored logarithm variants.
* Maintain the return type deduction of native operators.
* Maintain overflow behavior of native operators.
* Return a common sentinel for undefined operations.
* Avoid unnecessary computation.

## Log
Integer logarithms are implemented as repeated division of the value by the base (i.e. until a quotient of zero). Native C++ division is truncated. However logarithms allow only positive value and base. Truncation of a positive quotient is floored, so only floored and ceilinged functions are required to support the [three common rounding methods](Integer-Division-Unraveled).

Base 2 logarithm is repeated division by 2, so the right shift operator (`>>`) may be used as a performance optimization. While the compiler may optimize for division by 2 at run time, compiling for it explicitly precludes at least one condition and branch in the object code.

The logarithm of a base less than 2 or a value less than 1 is undefined. Apart from an overflow, there is no valid 0 result. So 0 is used as the invalid parameter sentinel. As with all native operators, overflow guards are left to the caller. The implementation may not *cause* an overflow that is not inherent in the parameterization.

Iteration counting implies that the the result type is a non-negative arbitrary integer.

## Power
Integer powers are implemented as repeated multiplication of the base by itself.

Base 2 power is repeated multiplication by 2, so the left shift operator (`<<`) may be used as a performance optimization. While the compiler may optimize for multiplication by 2 at run time, compiling for it explicitly precludes at least one condition and branch in the object code.

All integer power parameters are defined with the exception of `power(0, 0)`. Apart from an overflow, 0 is the valid result only for any power of 0. So 0 is used as the invalid parameter sentinel for consistency with the logarithm functions. As with all native operators, overflow guards are left to the caller. The implementation may not *cause* an overflow that is not inherent in the parameterization.

The value type is the result type as the value is multiplied by itself.
## Implementation
These templates determine the odd-ness, sign, and absolute value of a signed or unsigned integer type.
```cpp
#include <type_traits>

template <typename Integer, if_integer<Integer> = true>
constexpr bool is_odd(Integer value) noexcept
{
    return (value % 2) != 0;
}

template <typename Integer, if_signed_integer<Integer> = true>
constexpr bool is_negative(Integer value) noexcept
{
    return value < 0;
}

template <typename Integer, if_unsigned_integer<Integer> = true>
constexpr bool is_negative(Integer value) noexcept
{
    return false;
}

template <typename Integer,
    typename Absolute = std::make_unsigned<Integer>::type,
    if_signed_integer<Integer> = true>
constexpr Absolute absolute(Integer value) noexcept
{
    return is_negative(value) ? -value : value;
}

template <typename Integer,
    typename Absolute = Integer,
    if_unsigned_integer<Integer> = true>
constexpr Absolute absolute(Integer value) noexcept
{
    return value;
}
```
These templates implement the logarithm functions.
```cpp
// Returns 0 for undefined (base < 2 or value < 1).
template <typename Exponent = size_t, typename Base, typename Value,
    if_integer<Exponent> = true, if_integer<Base> = true, if_integer<Value> = true>
inline Exponent ceilinged_log(Base base, Value value) noexcept
{
    if (base < 2 || value < 1)
        return 0;

    const auto log = floored_log<Exponent>(base, value);
    return log + ((power<Value>(base, log) == value) ? 0 : 1);
}
    
// Returns 0 for undefined (value < 1).
template <typename Exponent = size_t, typename Value,
    if_integer<Exponent> = true, if_integer<Value> = true>
inline Exponent ceilinged_log2(Value value) noexcept
{
    if (value < 1)
        return 0;

    const auto log = floored_log2<Exponent>(value);
    return log + ((power2<Value>(log) == value) ? 0 : 1);
}

// Returns 0 for undefined (base < 2 or value < 1).
template <typename Exponent = size_t, typename Base, typename Value,
    if_integer<Exponent> = true, if_integer<Base> = true, if_integer<Value> = true>
inline Exponent floored_log(Base base, Value value) noexcept
{
    if (base < 2 || value < 1)
        return 0;

    Exponent exponent = 0;
    while (((value /= base)) > 0) { ++exponent; }
    return exponent;
}

// Returns 0 for undefined (value < 1).
template <typename Exponent = size_t, typename Value,
    if_integer<Exponent> = true, if_integer<Value> = true>
inline Exponent floored_log2(Value value) noexcept
{
    if (value < 1)
        return 0;

    Exponent exponent = 0;
    while (((value >>= 1)) > 0) { ++exponent; };
    return exponent;
}
```
These templates implement the power functions.
```cpp
// Returns 0 for undefined (0, 0).
template <typename Value, typename Base, typename Exponent,
    if_integer<Value> = true, if_integer<Base> = true, if_integer<Exponent> = true>
inline Value power(Base base, Exponent exponent) noexcept
{
    if (base == 0)
        return 0;

    if (exponent == 0)
        return 1;

    if (is_negative(exponent))
        return absolute(base) > 1 ? 0 :
            (is_odd(exponent) && is_negative(base) ? -1 : 1);

    Value value = base;
    while (--exponent > 0) { value *= base; }
    return value;
}

// This overload allows the return type to default to Base while not requiring
// the unnecessary Base parameter just to specify the Value return parameter.
template <typename Base, typename Exponent,
    if_integer<Base> = true, if_integer<Exponent> = true>
inline Base power(Base base, Exponent exponent) noexcept
{
    return power<Base>(base, exponent);
}

template <typename Value = size_t, typename Exponent,
    if_integer<Value> = true, if_integer<Exponent> = true>
inline Value power2(Exponent exponent) noexcept
{
    if (exponent == 0)
        return 1;

    if (is_negative(exponent))
        return 0;

    Value value = 2;
    while (--exponent > 0) { value <<= 1; };
    return value;
}
```

## Optimization
The use of `std::signbit` is avoided as [it casts](https://en.cppreference.com/w/cpp/numeric/math/signbit) to `double`, though otherwise would be sufficient to replace the `is_negative` templates.

The `inline` keyword advises the compiler that inlining of the functions is preferred. This removes call stack overhead, assuming the compiler respects the request. Generally we prefer to let the compiler make these decisions, preserving code readability.

> A compiler may warn (incorrectly) of division by zero "possibility" in a logarithm base `const` 0 test case, given that it is inlining an (unreachable) division by literal 0. Removal of the `inline` keyword can prevent this if desired, but the warning is beneficial for production (vs. test) code. A better alternative may be to use a non-const, zero-valued base variable in the logarithm test cases.

The `constexpr` keyword ensures that functions can be evaluated at compile time. In C++14 the inlined functions above can also be changed to `constexpr`. This also allows the functions to be integrated into template type constraints and `static_assert` expressions.

The following section of `power`, and the corresponding but reduced section of `power2`, implement three short-circuits that also serve as necessary guards for the `while` loops.
```cpp
    if (base == 0)
        return 0;

    if (exponent == 0)
        return 1;

    if (is_negative(exponent))
        return absolute(base) > 1 ? 0 :
            (is_odd(exponent) && is_negative(base) ? -1 : 1);

```
The `base == 0` guard takes advantage of the fact that 0 to any power is 0, while also serving up the sentinel value for the `power(0, 0)` undefined condition. The `exponent == 0` guard takes advantage of the fact that any value to the power of 0 is 1. The `is_negative(exponent)` guard takes advantage of the fact that any value to a negative power is between -1 and +1 (inclusive). Given integer operations, this is limited to { -1, 0, +1 }.

In each of the logarithm functions, there is a `base < 2` (as applicable) and `value < 1` condition. These are not optimizations, as they only determine parameter validity, but they do also serve as necessary guards for the `while` loops.

Both `power2(n)` and `floored_log2(n)` could simply call `power(2, n)` and `floored_log(2, n)` respectively. The implementations are nearly identical, with the exception of shift left or shift right vs. multiply by 2 or divide by 2. However there are optimizations in removing both unnecessary guards and the presumed run-time condition and branch to a shifted optimization.

Despite the relative verbosity of the templates the result should be as optimal as manually inlining the minimal *necessary* operations. A few runs through an NDEBUG build in a debugger confirm this.

## Template Type Constraints
Given that the C++ division operator determines the logarithm result type (based on the operand types) the return type must be so determined. This is achieved by using the C++14 `decltype` keyword.

#### C++14
Without the template overloads there would be warnings on unsigned type `power` operands, as they all invoke `value < 0`, which is always `false` (tautological). It is trivial to replace all of these calls with `value < 1` calls, as in each case `0` has been excluded. However, this materially impacts readability and there is no performance benefit. In fact, the inlined templates compile away for unsigned types whereas a `< 1`condition would not, so there is a performance advantage. For functions or operand combinations that are not referenced, the corresponding templates are not even compiled. The use of `std::abs` avoided as [it is limited](https://en.cppreference.com/w/cpp/numeric/math/abs) to signed types.

Template specialization could be further employed to reduce a couple calls when parameters are unsigned. However there is little to no actual performance optimization and the denormalization didn't seem like a worthwhile compromise.

The templates can be factored into header (.hpp) and implementation (.ipp) files, just be sure to remove the default template parameter values in the implementation.

See [Type Constraints Unraveled](Type-Constraints-Unraveled) for an explanation of the template type constraints used above.

## Mixing Unsigned and Signed Operands
There are no interesting consequences to the use of mixed sign operands.

## Test Vectors
Exponents and logarithms are [inverse functions](https://en.wikipedia.org/wiki/Inverse_function), so these relations must hold for all defined { b, n }, excepting overflows.

* `floored_log(b, power(b, n)) == n`.
* `ceilinged_log(b, power(b, n)) == n`.

## Conclusion
* Behavior satisfies the inverse relation for all sign combinations.
* Return type is deduced in C++14 and in C++11 can be specified.
* Behavior is consistent with native operators.
* The functions cannot *cause* overflows.
* There can be no "tautological compare" warnings from unsigned parameters.
* The functions return a common sentinel for undefined operations.
* Stack calls may be fully removed by inlining.
* All functions can be `constexpr` in C++14.
* The `is_negative` and `absolute` calls compile away for `unsigned` operators.
* The `is_odd`, `is_negative`, and `absolute` calls compile away for `unsigned` and `constexpr` parameters.
* All conditions compile away when the above render them tautological.
* All that remains is *necessary*.
