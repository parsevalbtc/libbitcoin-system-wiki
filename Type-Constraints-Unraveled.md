If one is not familiar with C++ template [overload resolution](https://en.cppreference.com/w/cpp/language/overload_resolution) the use of `enable_if` to constrain template parameters can be a little hard to follow.
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

// C++14: use std::enable_if_t.
template <bool Bool, typename Type=void>
using enable_if_t = typename std::enable_if<Bool, Type>::type;
```

```cpp
#include <limits>
#include <type_traits>

template <typename Base, typename Type>
using if_base_of = enable_if_t<
    std::is_base_of<Base, Type>::value, bool>;

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
When the `Bool` parameter of `enable_if_t<bool Bool, typename Type>` is true, `enable_if_t` resolves to the specified `Type`, in the above cases `bool`. Otherwise it resolves to the undefined expression (`struct enable_if{}::type`). The former is then defaulted, using `=true` (or `=false`) so that it is not required. The latter will not match any expression, so that case is excluded.

The following `is_negative` templates provide an example.
```cpp
template <typename Integer, if_signed_integer<Integer>=true>
inline bool is_negative(Integer value)
{
    return value < 0;
}

template <typename Integer, if_unsigned_integer<Integer>=true>
inline bool is_negative(Integer value)
{
    return false;
}
```
The above `is_negative` templates reduce to the following.
```cpp
// std::numeric_limits<Integer>::is_integer && std::numeric_limits<Integer>::is_signed
template <typename Integer, bool=true>
inline bool is_negative(Integer value)
{
    return value < 0;
}

// std::numeric_limits<Integer>::is_integer && !std::numeric_limits<Integer>::is_signed
template <typename Integer, bool=true>
inline bool is_negative(Integer value)
{
    return false;
}
```