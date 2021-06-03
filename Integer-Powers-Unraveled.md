```cpp
// Returns 0 for undefined (base < 2 or value < 1).
template <typename Base, typename Integer, typename Log,
    IS_INTEGER(Base), IS_INTEGER(Integer)>
Log ceilinged_log(Base base, Integer value)
{
    if (base < 2 || value < 1)
        return 0;

    return floored_log(base, value) + ((value % base) != 0 ? 1 : 0);
}
    
// Returns 0 for undefined (value < 1).
template <typename Integer, IS_INTEGER(Integer)>
Integer ceilinged_log2(Integer value)
{
    if (value < 1)
        return 0;

    return floored_log2(value) + (value % 2);
}

// Returns 0 for undefined (base < 2 or value < 1).
template <typename Base, typename Integer, typename Log,
    IS_INTEGER(Base), IS_INTEGER(Integer)>
Log floored_log(Base base, Integer value)
{
    if (base < 2 || value < 1)
        return 0;

    Log exponent = 0;
    while (((value /= base)) > 0) { ++exponent; }
    return exponent;
}

// Returns 0 for undefined value < 1).
template <typename Integer, IS_INTEGER(Integer)>
Integer floored_log2(Integer value)
{
    if (value < 1)
        return 0;

    Integer exponent = 0;
    while (((value >>= 1)) > 0) { ++exponent; };
    return exponent;
}

// Returns 0 for undefined (0^0).
template <typename Base, typename Integer, typename Power,
    IS_INTEGER(Base), IS_INTEGER(Integer)>
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

template <typename Integer, IS_INTEGER(Integer)>
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
## Type Constraints
```cpp
// Borrowing enable_if_t from c++14.
// en.cppreference.com/w/cpp/types/enable_if
template<bool Bool, class Type=void>
using enable_if_type = typename std::enable_if<Bool, Type>::type;

#define IS_INTEGER(Type) \
enable_if_type<std::numeric_limits<Type>::is_integer, bool>
```