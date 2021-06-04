Logarithms and powers (exponents) are occasionally useful in Bitcoin work, especially base 2. But C++ [log functions](https://en.cppreference.com/w/cpp/numeric/math/log) are all floating point. There is not much call for floating point calculation in Bitcoin, and I have a problem with using the wrong data types. It causes code bloat and warnings due to type casting, and can result in errors due to unexpected rounding behavior. Floating point operations may also be more CPU intensive than those necessary for integer operations. So I decided to clean up some code by implementing a proper suite of integer exponentiation functions.

Natural logs are not an objective as they are inherently floating point (base *e*). Logs of negative numbers are non-continuous functions, so that is also not an objective.

## Objectives
* Provide power and log, for all integer types.
* Provide simplified base 2 variants of power and log.
  * log2(n) identical in behavior to log(2, n).
  * power2(n) identical in behavior to power(2, n).
* Provide ceilinged_log and floored_log variants.
* Maintain the return type deduction of native operators.
* Maintain overflow behavior of native operators.
* Return zero for undefined operations.
* Avoid unnecessary computation.

## Log
Integer logarithms are implemented as repeated division of the value by the base (i.e. until a quotient of zero). Native C++ division is truncated. However logarithms allow only positive value and base. Truncation of a positive quotient is floored, so only floored and ceilinged functions are required to support the [three common rounding methods](Integer-Division-Unraveled).

Base 2 logarithm is repeated division by 2, so the right shift operator (`>>`) may be used as a performance optimization. While the compiler may optimize for division by 2 at run time, compiling for it explicitly precludes at least one condition and branch in the object code.

Log of a base less than 2 or a value less than 1 is undefined. Apart from an overflow, there is no valid 0 result. So 0 is used as the invalid parameter sentinel. As with all native operators, overflow guards are left to the caller. The implementation may not *cause* an overflow not inherent in the parameterization.

Division implies that the native division operator (`/`) determines the result type, based on its operand types.

## Power
Integer powers are implemented as repeated multiplication of the base by itself.

Base 2 power is repeated multiplication by 2, so the left shift operator (`<<`) may be used as a performance optimization. While the compiler may optimize for multiplication by 2 at run time, compiling for it explicitly precludes at least one condition and branch in the object code.

All integer power parameters are defined with the exception of `power(0, 0)`. Apart from an overflow, 0 is the valid result only for any power of 0. So 0 is used as the invalid parameter sentinel for consistency with the log functions. As with all native operators, overflow guards are left to the caller. The implementation may not *cause* an overflow not inherent in the parameterization.

The value type is the result type as the value is multiplied by itself.
## Implementation
```cpp
// Returns 0 for undefined (base < 2 or value < 1).
template <typename Base, typename Integer, typename Log,
    IS_INTEGER(Base)=true, IS_INTEGER(Integer)=true>
Log ceilinged_log(Base base, Integer value)
{
    if (base < 2 || value < 1)
        return 0;

    return floored_log(base, value) + ((value % base) != 0 ? 1 : 0);
}
    
// Returns 0 for undefined (value < 1).
template <typename Integer,
    IS_INTEGER(Integer)=true>
Integer ceilinged_log2(Integer value)
{
    if (value < 1)
        return 0;

    return floored_log2(value) + (value % 2);
}

// Returns 0 for undefined (base < 2 or value < 1).
template <typename Base, typename Integer, typename Log,
    IS_INTEGER(Base)=true, IS_INTEGER(Integer)=true>
Log floored_log(Base base, Integer value)
{
    if (base < 2 || value < 1)
        return 0;

    Log exponent = 0;
    while (((value /= base)) > 0) { ++exponent; }
    return exponent;
}

// Returns 0 for undefined (value < 1).
template <typename Integer,
    IS_INTEGER(Integer)=true>
Integer floored_log2(Integer value)
{
    if (value < 1)
        return 0;

    Integer exponent = 0;
    while (((value >>= 1)) > 0) { ++exponent; };
    return exponent;
}

// Returns 0 for undefined (0, 0).
template <typename Base, typename Integer, typename Power,
    IS_INTEGER(Base)=true, IS_INTEGER(Integer)=true>
Power power(Base base, Integer exponent)
{
    if (base == 0)
        return 0;

    if (exponent == 0)
        return 1;

    if (is_negative(exponent))
        return absolute(base) > 1 ? 0 :
            (is_odd(exponent) && is_negative(base) ? -1 : 1);

    Power value = base;
    while (--exponent > 0) { value *= base; }
    return value;
}

template <typename Integer,
    IS_INTEGER(Integer)=true>
Integer power2(Integer exponent)
{
    if (exponent == 0)
        return 1;

    if (is_negative(exponent))
        return 0;

    Integer value = 2;
    while (--exponent > 0) { value <<= 1; };
    return value;
}
```
## Optimization
The use of `std::signbit` is avoided as [it casts](https://en.cppreference.com/w/cpp/numeric/math/signbit) to `double`, though otherwise would be sufficient to replace the `negative` templates.

The `inline` keyword advises the compiler that inlining of the functions is preferred. This removes call stack overhead, assuming the compiler respects the request. Generally I prefer to let the compiler make these decisions, preserving code readability. Inlining is avoided on the primary functions as they are non-trivial, though they may certainly be inlined.

> Also, a compiler may warn (incorrectly) of division by zero possibility in `*_log(0, n)` test cases, given that it is inlining an (unreachable) literal division by 0.

Despite the relative verbosity of the templates the result should be as optimal as manually inlining the minimal *necessary* operations. A few runs through an NDEBUG build in a debugger confirm this.

## Template Type Constraints
Given that the C++ division operator determines the log result type (based on the operand types) the return type must be so determined. This is achieved by using the C++14 `decltype` keyword.

#### C++14
* `typename Log=decltype(Integer / Base)`

In C++11 the return type can be explicitly specified by template parameter, and is defaulted in the above templates to the value type.

#### C++11
* `typename Log=Integer`

Without the template overrides there would be warnings on power operands, as they all invoke `factor < 0`, which is always `false` (tautological). These also bypass unnecessary conditions at compile time. For functions or operand combinations that are not referenced, the corresponding templates are not even compiled.

Template specialization could be further employed to reduce a couple calls when both parameters are unsigned. However there is little to no actual performance optimization and the denormalization didn't seem like a worthwhile compromise.

The templates can be factored into header (.hpp) and implementation (.ipp) files, just be sure to remove the default template parameter values in the implementation.

These are the type constraint macros used above. As a rule I make very limited use of macros. But these improve readability and maintainability by reducing repetition, without impacting debugging. These will work on any C++11 or later compiler.
```cpp
// Borrowing enable_if_t from c++14.
// en.cppreference.com/w/cpp/types/enable_if
template<bool Bool, class Type=void>
using enable_if_type = typename std::enable_if<Bool, Type>::type;

#define IS_INTEGER(Type) \
enable_if_type<std::numeric_limits<Type>::is_integer, bool>
```

## Test Vectors
Power and log are inverse functions, so these relations must hold, for both `ceilinged_log` and `floored_log`, excepting overflows.

* for all defined {b, n}, `log(b, power(b, n)) == n`.
* for all defined {b, n}, where `(n % b) == 0`, `power(b, log(b, n)) == n`.

Additionally these relations must hold for the implementation.

* for all defined {b, n} where `(n % b) == 0`, `ceilinged_log == floored_log`.
* for all defined {b, n} where `(n % b) != 0`, `ceilinged_log == floored_log + 1`.
* for all undefined {b, n}, log and power must return `0`.

