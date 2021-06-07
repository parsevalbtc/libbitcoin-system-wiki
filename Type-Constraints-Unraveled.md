If one is not familiar with C++ template [overload resolution](https://en.cppreference.com/w/cpp/language/overload_resolution), the use of `enable_if` and `enable_if_t` to constrain template parameters can be a little hard to follow. While these are [provided by](https://en.cppreference.com/w/cpp/types/enable_if) C++11 and C++14 respectively, they are trivially implemented as follows.
```cpp
// C++11 implementation of std::enable_if.
template<bool Bool, typename Type = void>
struct enable_if
{
};
 
template<class Type>
struct enable_if<true, Type>
{
    typedef Type type;
};

// C++14 implementation of use std::enable_if_t.
template <bool Bool, typename Type = void>
using enable_if_t = typename enable_if<Bool, Type>::type;
```
The following helpers combine signed-ness with integer-ness (i.e. [floating point excluded](https://en.cppreference.com/w/cpp/types/numeric_limits/is_integer)).
```cpp
#include <type_traits>

template <typename Type>
using if_integer = enable_if_t<
    std::is_integral<Type>::value, bool>;

template <typename Type>
using if_signed_integer = enable_if_t<
    std::is_integral<Type>::value &&
    std::is_signed<Type>::value, bool>;

template <typename Type>
using if_unsigned_integer = enable_if_t<
    std::is_integral<Type>::value &&
    !std::is_signed<Type>::value, bool>;
```
* `std::is_integral<Type>` and `std::is_signed<Type>` require C++11
* For older versions of C++ these may be implemented with simple [custom templates](https://en.cppreference.com/w/cpp/types/is_signed).
* Use of `std::is_integral_v` or `std::is_signed_v` (which emit `::value`) would require C++17.
* Use of `std::numeric_limits<Type>` would require C++11 for `long long` and `unsigned long long`

When the `Bool` parameter of `enable_if_t<bool Bool, typename Type>` is true, `enable_if_t` resolves to the specified `Type`, in the above cases `bool`. Otherwise it resolves to the undefined expression `(struct enable_if{})::type`. The former is then defaulted (i.e. using `= true` or `= false`) so that it is not required. The latter will not match any expression, so that case is excluded.

## Using enable_if_t

The following `is_negative` overloads provide an example.
```cpp
template <typename Integer, if_signed_integer<Integer> = true>
bool is_negative(Integer value)
{
    return value < 0;
}

template <typename Integer, if_unsigned_integer<Integer> = true>
bool is_negative(Integer value)
{
    return false;
}
```
These reduce to the following.
```cpp
// if (std::is_integral<Integer>::value && std::is_signed<Integer>::value)
template <typename Integer, bool = true>
bool is_negative(Integer value)
{
    return value < 0;
}

// if (std::is_integral<Integer>::value && !std::is_signed<Integer>::value)
template <typename Integer, bool = true>
bool is_negative(Integer value)
{
    return false;
}
```
A side effect of this technique is that the signature of `is_negative` is actually `is_negative<Integer, bool>(Integer value)`, where a `bool` value is ignored if specified.

## Using enable_if

Another approach is to reply on `enable_if` alone.
```cpp
template <typename Integer,
    typename Unused = enable_if<std::is_integral<Integer>::value>::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
As the `Unused` type may be unnamed, this may also be written as follows. 
```cpp
template <typename Integer,
    typename = enable_if<std::is_integral<Integer>::value>::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
This resolves to the following.
```cpp
// if (std::is_integral<Integer>::value)
template <typename Integer,
    typename = (struct enable_if{ typedef bool type; })::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

// if (!std::is_integral<Integer>::value)
template <typename Integer,
    typename = (struct enable_if{})::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
And these reduce to the following.
```cpp
// if (std::is_integral<Integer>::value)
template <typename Integer, typename = bool>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

// if (!std::is_integral<Integer>::value)
template <typename Integer, typename = undefined>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
The `bool` type is inferred from the expression `std::is_integral<Integer>::value` which was passed to `enable_if`, exposed by `enable_if` via its `::type` declaration, and then dereferenced by `::type` in the template declaration.

As the latter will not match any expression, the former remains. Therefore the signature is actually `is_odd<Integer, typename = bool>(Integer value)`, where the second template parameter may be any type and is ignored if specified.

## Common Mistakes
These compile but **do not** constrain the type.
```cpp
template <typename Integer, typename = enable_if_t<std::numeric_limits<Integer>::is_integer>>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

template <typename Integer, enable_if<std::numeric_limits<Integer>::is_integer>::type = true>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
These reduce respectively to the following, under all conditions.
```cpp
template <typename Integer, typename = bool>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

template <typename Integer, bool = true>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
Consequently, any type of value passed to `is_odd(Type value)` will compile if there is a `Type % 2` operator overload for the type. Natively this includes all [arithmetic types](https://en.cppreference.com/w/c/language/arithmetic_types) (including char and floating point) but may include others.