If one is not familiar with C++ template [overload resolution](https://en.cppreference.com/w/cpp/language/overload_resolution), the use of `enable_if` and `enable_if_t` to constrain template parameters can be a little hard to follow.
```cpp
// C++11 implementation of std::enable_if.
template<bool Bool, typename Type=void>
struct enable_if
{
};
 
template<class Type>
struct enable_if<true, Type>
{
    typedef Type type;
};

// C++14 implementation of use std::enable_if_t.
template <bool Bool, typename Type=void>
using enable_if_t = typename enable_if<Bool, Type>::type;
```
The following helpers combine signed-ness with integer-ness (i.e. floating point excluded).
```cpp
#include <limits>
#include <type_traits>

template <typename Type>
using if_integer = enable_if_t<
    std::numeric_limits<Type>::is_integer, bool>;

template <typename Type>
using if_signed_integer = enable_if_t<
    std::numeric_limits<Type>::is_integer &&
    std::numeric_limits<Type>::is_signed, bool>;

template <typename Type>
using if_unsigned_integer = enable_if_t<
    std::numeric_limits<Type>::is_integer &&
    !std::numeric_limits<Type>::is_signed, bool>;
```
When the `Bool` parameter of `enable_if_t<bool Bool, typename Type>` is true, `enable_if_t` resolves to the specified `Type`, in the above cases `bool`. Otherwise it resolves to the undefined expression `(struct enable_if{})::type`. The former is then defaulted (i.e. using `=true` or `=false`) so that it is not required. The latter will not match any expression, so that case is excluded.

## Using enable_if_t

The following `is_negative` overloads provide an example.
```cpp
template <typename Integer, if_signed_integer<Integer>=true>
bool is_negative(Integer value)
{
    return value < 0;
}

template <typename Integer, if_unsigned_integer<Integer>=true>
bool is_negative(Integer value)
{
    return false;
}
```
These reduce to the following.
```cpp
// if (std::numeric_limits<Integer>::is_integer && std::numeric_limits<Integer>::is_signed)
template <typename Integer, bool=true>
bool is_negative(Integer value)
{
    return value < 0;
}

// if (std::numeric_limits<Integer>::is_integer && !std::numeric_limits<Integer>::is_signed)
template <typename Integer, bool=true>
bool is_negative(Integer value)
{
    return false;
}
```
A side effect of this technique is that the signature of `is_negative` is actually `is_negative<Integer, bool>(Integer value)`, where a `bool` value is ignored if specified.

## Using enable_if

Another approach is to reply on `enable_if` alone.
```cpp
template <typename Integer, typename Unused = enable_if<std::numeric_limits<Integer>::is_integer>::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
As the `Unused` type may be unnamed, this may also be written as follows. 
```cpp
template <typename Integer, typename = enable_if<std::numeric_limits<Integer>::is_integer>::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
These reduce to the following.
```cpp
// if (std::numeric_limits<Integer>::is_integer)
template <typename Integer, typename = (struct enable_if{ typedef bool type; })::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

// if (!std::numeric_limits<Integer>::is_integer)
template <typename Integer, typename = (struct enable_if{})::type>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
And further reduce to the following.
```cpp
// if (std::numeric_limits<Integer>::is_integer)
template <typename Integer, typename = bool>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}

// if (!std::numeric_limits<Integer>::is_integer)
template <typename Integer, typename = undefined>
bool is_odd(Integer value)
{
    return (value % 2) != 0;
}
```
The `bool` type is inferred from the expression `std::numeric_limits<Integer>::is_integer` which was passed to `enable_if`, exposed by `enable_if` via its `::type` declaration, and then dereferenced by `::type` in the first template.

As the latter will not match any expression, the former remains. Therefore the signature is actually `is_odd<Integer, typename = bool>(Integer value)`, where the second template parameter may be any type and is ignored if specified.