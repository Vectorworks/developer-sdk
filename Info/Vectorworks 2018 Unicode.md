# General Information

## TXString

TXString has been re-implemented in Vectorworks 2018 to be Unicode compliant. Most methods and behaviors of TXString have been preserved while internal implementation of them has been replaced. In addition, some new methods have also been added to make TXString a more modern and powerful string class. The native element of TXString is of TXChar type, which uses UTF-16 encoding and occupies two bytes.

### Formatting

The `TXString::Format` and `TXString::VFormat` functions have been removed.

They are replaced by the type-safe functions `TXString::Format` and `static TXString::Formatted`. These functions require known types to be used: 

- `short`, `unsigned short`
- `int`, `unsigned int`
- `long`, `unsigned long`
- `long long`, `unsigned long long`
- `double`
- `const char`
- `const char*` -- a UTF-8 encoded string
- `const wchar_t`
- `const wchar_t*` -- this type depends on the OS, UTF-16 on Windows and UTF-32 on Mac.
- `const void*` -- a generic pointer
- `TXString`

They support the `%` markers: `d`, `i`, `u`, `o`, `x`, `X`, `f`, `F`, `e`, `E`, `g`, `G`, `a`, `A`, `c`, `s`, `p`, `n`

### Numeric Conversions

The functions to convert the string to a number have been preserved:

```cpp
Sint32 atoi() const;
Sint64 atoi64() const;
Real64 atof() const;
```

Additional functions for number to string have been added:

```cpp
TXString&       itoa(Sint64 value);
TXString&       ftoa(Real64 value);
TXString&       ftoa(Real64 value, Sint32 precision);
static TXString ToStringInt(Sint64 value);
static TXString ToStringInt(Sint64 value, Sint32 width, wchar_t paddingChar);
static TXString ToStringReal(Real64 value);
static TXString ToStringReal(Real64 value, Sint32 precision, bool fixed=true);
template<typename T> static TXString ToString(T value);
template<typename T> static TXString ToStringHex(T value, bool upperCase = true, size_t symbolsCnt = size_t(-1));
