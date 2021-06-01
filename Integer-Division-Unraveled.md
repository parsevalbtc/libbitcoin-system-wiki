Sometimes the most trivial coding tasks can be unexpectedly challenging. In working with Python equivalence I found it necessary to implement floored signed integer modulo in C++.

A quick stop at Stack Overflow shows how confounding this can be. There are several different approaches, most of which either do not support signed operands, cause overflows, throw exceptions, and/or just don't work. Even the "accepted" answers are flawed in such ways, with large numbers of upvotes. So I decided to just figure it out myself.

## Objectives

* Provide ceilinged and floored `\` and `%` functions, for all integer types.
* Maintain the failure behavior of native operators (i.e. `x / 0` and `x % 0`)
* Maintain overflow behavior of native operators.
* Avoid all unnecessary computation.

## Analysis

The key is in understanding the nature of the native operators. Integer division is actually a challenging subject. Programming languages implement it in [different ways](https://en.wikipedia.org/wiki/Modulo_operation). Python actually [changed the behavior](https://www.python.org/dev/peps/pep-0238/) of `\` and `%` in a minor version release! This of course led to additional scrutiny in the original Python equivalence task.

> We propose to fix this by introducing different operators for different operations: x/y to return a reasonable approximation of the mathematical result of the division ("true division"), x//y to return the floor ("floor division"). We call the current, mixed meaning of x/y "classic division".

C++ implements `truncated` integer division. Python implements `floored` integer division. The other common form (`ceilinged`) is quite useful as well. Common hacks for ceilinged division are:

```cpp
// This overflows and is limited to positive integers.
auto q = (x + (y - 1)) / y;

// This is limited to positive integers.
auto q = (x / y) + !!(x % y);

// This warns on unsigned types.
auto q = x / y + (((x < 0) != (y < 0)) && (x % y);
```

There is no distinction unless there is a remainder - all rounding methods return the same result. But integer division must discard the remainder. So the question becomes how to consistently round the quotient. All modulo operations will necessarily be consistent with this choice of quotient rounding, which means that they vary as well.

Truncated division simply drops the remainder. So in the case of a positive quotient, the quotient is floored (less positive) and in the case of a negative quotient, the quotient is ceilinged (less negative). This is also called "toward zero" rounding. In changing rounding behavior, one must know `whether there is a remainder` (and its magnitude when implementing `%`) and `the sign of the quotient`. The quotient is positive if both operands have the same sign, otherwise it is negative.

## Implementation

These templates answer the above two questions.

```cpp
// These eliminate (unsigned < 0) warnings (always false) and compile away the
// conditions entirely when unsigned (optimization).
// ----------------------------------------------------------------------------
template <typename Integer,
    IS_UNSIGNED_INTEGER(Integer)=true>
inline bool negative(Integer)
{
    return false;
}

template <typename Integer,
    IS_SIGNED_INTEGER(Integer)=true>
inline bool negative(Integer value)
{
    // The comparison compiles away for constexpr values.
    return value < 0;
}

template <typename Factor1, typename Factor2,
    IS_INTEGERS(Factor1, Factor2)=true>
inline bool negative(Factor1 factor1, Factor2 factor2)
{
    return negative(factor1) != negative(factor2);
}
// ----------------------------------------------------------------------------

template <typename Dividend, typename Divisor,
    IS_INTEGERS(Dividend, Divisor)=true>
inline bool remainder(Dividend dividend, Divisor divisor)
{
    // The modulo compiles away for constexpr values.
    return (dividend % divisor) != 0;
}
```
These two templates combine them into single answer.
```cpp
template <typename Dividend, typename Divisor,
    IS_INTEGERS(Dividend, Divisor)=true>
inline bool ceilinged(Dividend dividend, Divisor divisor)
{
    // These checks compile away for constexpr values.
    // The negative template type constraints eliminate one or two compares.
    // No change unless remainder, and c++ mod ceilinged for negative quotient.
    return !remainder(dividend, divisor) || negative(dividend, divisor);
}

template <typename Dividend, typename Divisor,
    IS_INTEGERS(Dividend, Divisor)=true>
inline bool floored(Dividend dividend, Divisor divisor)
{
    // These checks compile away for constexpr values.
    // The negative template type constraints eliminate one or two compares.
    // No change unless remainder, and c++ mod floored for positive quotient.
    return !remainder(dividend, divisor) || !negative(dividend, divisor);
}
```
These templates implement the three common rounding approaches.
```cpp
template <typename Dividend, typename Divisor, Remainder=Dividend
    IS_INTEGERS(Dividend, Divisor)=true>
inline Remainder ceilinged_modulo(Dividend dividend, Divisor divisor)
{
    // truncated_modulo is positive if not ceilinged.
    return ceilinged(dividend, divisor) ?
        truncated_modulo(dividend, divisor) :
        divisor + truncated_modulo(dividend, divisor);
}

template <typename Dividend, typename Divisor, Quotient=Dividend
    IS_INTEGERS(Dividend, Divisor)=true>
inline Quotient ceilinged_divide(Dividend dividend, Divisor divisor)
{
    // truncated_divide is 1 low if not ceilinged. 
    return ceilinged(dividend, divisor) ?
        truncated_divide(dividend, divisor) :
        truncated_divide(dividend, divisor) + 1;
}
```
```cpp
// Override for unsigned floor (native operation).
// ----------------------------------------------------------------------------
template <typename Dividend, typename Divisor, Remainder=Dividend
    IS_UNSIGNED_INTEGERS(Dividend, Divisor)=true>
inline Remainder floored_modulo(Dividend dividend, Divisor divisor)
{
    // truncated_modulo is already floored for positive quotient.
    return truncated_modulo(dividend, divisor);
}
// ----------------------------------------------------------------------------

template <typename Dividend, typename Divisor, Remainder=Dividend
    IS_EITHER_INTEGER_SIGNED(Dividend, Divisor)=true>
inline Remainder floored_modulo(Dividend dividend, Divisor divisor)
{
    // truncated_modulo is negative if not floored.
    return floored(dividend, divisor) ?
        truncated_modulo(dividend, divisor) :
        divisor + truncated_modulo(dividend, divisor);
}

// Override for unsigned floor (native operation).
// ----------------------------------------------------------------------------
template <typename Dividend, typename Divisor, Quotient=Dividend
    IS_UNSIGNED_INTEGERS(Dividend, Divisor)=true>
inline Quotient floored_divide(Dividend dividend, Divisor divisor)
{
    // truncated_modulo is already floored for positive quotient.
    return truncated_divide(dividend, divisor);
}
// ----------------------------------------------------------------------------

template <typename Dividend, typename Divisor, Quotient=Dividend
    IS_EITHER_INTEGER_SIGNED(Dividend, Divisor)=true>
inline Quotient floored_divide(Dividend dividend, Divisor divisor)
{
    // truncated_divide is 1 high if not floored. 
    return floored(dividend, divisor) ?
        truncated_divide(dividend, divisor) :
        truncated_divide(dividend, divisor) - 1;
}
```
```cpp
template <typename Dividend, typename Divisor, Remainder=Dividend
    IS_INTEGERS(Dividend, Divisor)=true>
inline Remainder truncated_modulo(Dividend dividend, Divisor divisor)
{
    // C++ applies "toward zero" integer division rounding (and remainder).
    // Floored for positive quotient, ceilinged for negative quotient.
    return dividend % divisor;
}

template <typename Dividend, typename Divisor, Quotient=Dividend
    IS_INTEGERS(Dividend, Divisor)=true>
inline Quotient truncated_divide(Dividend dividend, Divisor divisor)
{
    // C++ applies "toward zero" integer division rounding (and remainder).
    // Floored for positive quotient, ceilinged for negative quotient.
    return dividend / divisor;
}
```
Return value types default to the dividend type and can be specified by explicit template parameter.

## Mixing Unsigned and Signed Operands
It is an objective is to reproduce native operand behavior, changing only the rounding. The native operators allow mixed sign types, although compilers will warn that the signed operand will be converted to unsigned. The warning is limited to literals, so I was unable to reproduce that aspect. However the execution behavior is identical, as all division and modulo operations are executed in the original sign type against the native operators. The consequence is that when mixing signed and unsigned *type* operands, the operation is unsigned. The same values will produce different results based on the type alone. The behavior is certainly not intuitive, so I spent hours making sure that the test cases were valid.

## Optimization
The `%` operator may be invoked twice in `floored_modulo` (with signed operands) and `ceilinged_modulo`. The first tests for remainder and the second produces it. It does not seem worth denormalizing the implementation by adding a variable to cache the value in the (sometimes) case where there is a non-zero remainder. So in these cases I am relying on CPU cache and/or compiler optimization to avoid remainder recomputation.

The `inline` keyword advises the compiler that inlining of the functions is preferred. This removes call stack overhead, assuming the compiler respects the request. Generally I prefer to let the compiler make these decisions, preserving code readability.

The compiler is expected to reduce the redundant `truncated_divide` calls in `ceilinged_divide` and `floored_divide`, and `truncated_modulo` calls in `ceilinged_modulo` and `floored_modulo`. However doing it explicitly is at best an object code *size* optimization, not a performance (computation) optimization.

Despite the relative verbosity of the templates the result should be as optimal as manually inlining the minimal *necessary* operations. A few runs through an NDEBUG build in a debugger confirm this.

## Conclusion
* The `negative` calls compile away for `unsigned` operands.
* The `remainder` and `negative` calls compile away for `constexpr` operators.
* The `||` conditions compile away when the above render the result always `true` or `false`.
* The functions *do* fail as native operators with a zero-valued divisor.
* The functions cannot *cause* overflows.
* The stack calls are removed by inlining.
* All that remains is *necessary*.

By fanning out templates based on signed-ness type constraints, the objective of a "single" function that supports all integers without limitation or overhead is achieved.

## Template Type Constraints
Without the template overrides there would be warnings on unsigned operands, as they all invoke `factor < 0`, which is always `false`. These and the overrides for `floored_modulo` and `floored_divide` bypass unnecessary conditions at compile time. For functions or operand combinations that are not referenced, the corresponding templates are not even compiled.

The templates can be factored into header (.hpp) and implementation (.ipp) files, just be sure to remove the `=true` default template parameter values in the implementation.

These are the type constraint macros used above. As a rule I make very limited use of macros. But these significantly improve readability and maintainability by reducing repetition, without impacting debugging. These will work on any C++11 or later compiler.
```cpp
#include <limits>
#include <type_traits>

// Borrowing enable_if_t from c++14.
// en.cppreference.com/w/cpp/types/enable_if
template<bool Bool, class Type=void>
using enable_if_type = typename std::enable_if<Bool, Type>::type;

#define IS_UNSIGNED_INTEGER(Type) \
enable_if_type< \
    std::numeric_limits<Type>::is_integer && \
    !std::numeric_limits<Type>::is_signed, bool>

#define IS_SIGNED_INTEGER(Type) \
enable_if_type< \
    std::numeric_limits<Type>::is_integer && \
    std::numeric_limits<Type>::is_signed, bool>

#define IS_INTEGERS(Left, Right) \
enable_if_type< \
    std::numeric_limits<Left>::is_integer && \
    std::numeric_limits<Right>::is_integer, bool>

#define IS_UNSIGNED_INTEGERS(Left, Right) \
enable_if_type< \
    std::numeric_limits<Left>::is_integer && \
    !std::numeric_limits<Left>::is_signed && \
    std::numeric_limits<Right>::is_integer && \
    !std::numeric_limits<Right>::is_signed, bool>

#define IS_EITHER_INTEGER_SIGNED(Left, Right) \
enable_if_type< \
    (std::numeric_limits<Left>::is_integer && \
        std::numeric_limits<Left>::is_signed) || \
    (std::numeric_limits<Right>::is_integer && \
        std::numeric_limits<Right>::is_signed), bool>
```
## Math
```cpp
// C++11: if the quotient x/y is representable in the type of the result:
// Identity: (x / y) * y + (x % y) = x

// Divide increment magnitude.
constexpr auto i = 1;

// Operand magnitudes.
constexpr auto x = 4;
constexpr auto y = 3;

// Zero to zero.
static_assert(0 / -y == 0, "0");
static_assert(0 / +y == 0, "0");
static_assert(0 % -y == 0, "0");
static_assert(0 % +y == 0, "0");

// ----------------------------------------------------------------------------

// Truncated divide:
static_assert(+x / +y == +1, "+");
static_assert(-x / -y == +1, "+");
static_assert(-x / +y == -1, "-");
static_assert(+x / -y == -1, "-");

// Truncated modulo:
static_assert(+x % +y == +1, "+");
static_assert(-x % -y == -1, "+");
static_assert(-x % +y == -1, "-");
static_assert(+x % -y == +1, "-");

// Truncated identity:
static_assert((+x / +y) * (+y) + (+x % +y) == +x, "+");
static_assert((-x / -y) * (-y) + (-x % -y) == -x, "+");
static_assert((-x / +y) * (+y) + (-x % +y) == -x, "-");
static_assert((+x / -y) * (-y) + (+x % -y) == +x, "-");

// ----------------------------------------------------------------------------

// Ceilinged divide: (increment magnitude of quotient if remainder)
static_assert((+x / +y) + (((+x < 0) == (+y < 0)) ? ((+x % +y) != 0 ? +i : 0) : 0) == +1 + i, "+");
static_assert((-x / -y) + (((-x < 0) == (-y < 0)) ? ((-x % -y) != 0 ? +i : 0) : 0) == +1 + i, "+");
static_assert((-x / +y) + (((-x < 0) == (+y < 0)) ? ((-x % +y) != 0 ? +i : 0) : 0) == -1 + 0, "-");
static_assert((+x / -y) + (((+x < 0) == (-y < 0)) ? ((+x % -y) != 0 ? +i : 0) : 0) == -1 + 0, "-");

// Ceilinged modulo: (decrease magnitude of modulo by divisor if remainder)
static_assert((+x % +y) - (((+x < 0) == (+y < 0)) ? +y : 0) == +1 - +y, "+");
static_assert((-x % -y) - (((-x < 0) == (-y < 0)) ? -y : 0) == -1 - -y, "+");
static_assert((-x % +y) - (((-x < 0) == (+y < 0)) ? +y : 0) == -1 - +0, "-");
static_assert((+x % -y) - (((+x < 0) == (-y < 0)) ? -y : 0) == +1 - -0, "-");

// Ceilinged identity
static_assert((+1 + i) * (+y) + (+1 - +y) == +x, "+");
static_assert((+1 + i) * (-y) + (-1 - -y) == -x, "+");
static_assert((-1 + 0) * (+y) + (-1 - +0) == -x, "-");
static_assert((-1 + 0) * (-y) + (+1 - -0) == +x, "-");

// ----------------------------------------------------------------------------

// Floored divide: (increment magnitude of quotient if remainder)
static_assert((+x / +y) - (((+x < 0) != (+y < 0)) ? ((+x % +y) != 0 ? +i : 0) : 0) == +1 - 0, "+");
static_assert((-x / -y) - (((-x < 0) != (-y < 0)) ? ((-x % -y) != 0 ? +i : 0) : 0) == +1 - 0, "+");
static_assert((-x / +y) - (((-x < 0) != (+y < 0)) ? ((-x % +y) != 0 ? +i : 0) : 0) == -1 - i, "-");
static_assert((+x / -y) - (((+x < 0) != (-y < 0)) ? ((+x % -y) != 0 ? +i : 0) : 0) == -1 - i, "-");

// Floored modulo: (increase magnitude of modulo by divisor if remainder)
static_assert((+x % +y) + (((+x < 0) != (+y < 0)) ? +y : 0) == +1 + +0, "+");
static_assert((-x % -y) + (((-x < 0) != (-y < 0)) ? -y : 0) == -1 + +0, "+");
static_assert((-x % +y) + (((-x < 0) != (+y < 0)) ? +y : 0) == -1 + +y, "-");
static_assert((+x % -y) + (((+x < 0) != (-y < 0)) ? -y : 0) == +1 + -y, "-");

// Floored identity:
static_assert((+1 - 0) * (+y) + (+1 + +0) == +x, "+");
static_assert((+1 - 0) * (-y) + (-1 + +0) == -x, "+");
static_assert((-1 - i) * (+y) + (-1 + +y) == -x, "-");
static_assert((-1 - i) * (-y) + (+1 + -y) == +x, "-");
```