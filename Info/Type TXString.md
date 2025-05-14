# What is this?

**TXString** is one of the SDK generic types. It is used to represent a string in Vectorworks. TXString is Unicode-compliant since Vectorworks 2018.

## TXChar

TXString uses UTF-16 encoding since this encoding is natively used on Windows and Mac. There is a new typedef called **TXChar**, which is `wchar_t` on Windows and `UniChar` on Mac. Thus, TXChar uses UTF-16 encoding on both platforms.

TXString uses `std::basic_string<TXChar>` internally to store Unicode characters. There are two new macros for creating Unicode character or string literals. The following are two examples:

```cpp
TXString txStr1 = txuc('&#x1F60A;');             // Unicode character
TXString txStr2 = txu("1文本2テキ3ßÜü4");  // Unicode string literal
```

## Conversion to and from OS Native String Types

Using OS-dependent components (e.g., types, functions, classes, etc.) should be avoided. If the code has the need to handle OS native string types, the following are examples to achieve that.

### Windows

```cpp
// You can use the following code if MFC is used.
// CString → TXString
TXString txStr = cStr;

// TXString → CString
CString cStr = txStr.GetData();
```

### Mac

```cpp
// CFString
// CFStringRef → TXString
TXString txStr = cfStr;

// TXString → CFStringRef
CFStringRef cfStr = txStr.GetCFStringRef();
if (cfStr) {
    ... // Do something with cfStr;
    CFRelease(cfStr);
}

// NSString
// NSString → TXString
TXString txStr = [nsStr toTXString];

// TXString → NSString
NSString* nsStr = [NSString stringWithTXString:txStr];
```

## Precomposed and Decomposed Forms

Some Unicode characters can be represented in two forms: decomposed form and precomposed form (also called composite form). For example, 'パ' can be represented by one Unicode character (U+30D1, precomposed form) or by two Unicode characters (U+30CF ('ハ') + U+309A ('°'), decomposed form). Internally, Vectorworks uses Unicode characters in precomposed form. Client code using VW SDK doesn't need to worry about the difference.

Developers need to pay attention to the difference when calling some native Mac functions. In most cases, characters from OS APIs (Mac or Windows) are in the precomposed form. There are some exceptions on Mac, such as APIs handling URLs and plist files. When TXString objects are initiated from objects returned by OS in these areas, `ToPrecomposed()` can be called to convert decomposed form to precomposed form. If the code needs to return a string in decomposed form, `ToDecomposed()` can be called to do the conversion before sending it to the OS.

For example, SDK file/folder classes convert decomposed strings from Mac APIs to precomposed form right after these calls. Thus, all strings processed and manipulated internally are in precomposed form, and the client code using VW SDK doesn't need to worry about this. (If strings are in different forms, string comparison won't work. In addition, methods like element access operator `[]` and `GetLength()` may return different results if strings are in decomposed form.)

## Surrogate Pairs

Most Unicode characters can be represented by one UTF-16 character. However, Unicode characters greater than U+FFFF need two UTF-16 characters, called surrogate pairs. For example, Unicode character 'SMILING FACE WITH SMILING EYES' (&#x1F60A;, U+1F60A) is represented by 0xD83D and 0xDE0A in UTF-16. Thus, it is possible to have a TXString with invalid UTF-16 characters at the edges after some manipulations, such as `Truncate(...)`. TXString automatically removes invalid UTF-16 characters at the edges during the call to `Mid(...)`, `Left(...)`, `Right(...)`, or `Truncate(...)`. The client code can also call `TrimLeftInvalidCharacter()`, `TrimRightInvalidCharacter()`, or `TrimInvalidCharacters()` manually to remove invalid UTF-16 characters at the edges.

## Definition

The TXString class is defined in `"GSString.X.h"`.

### Constructors and Destructors

```cpp
    TXString();                     // Default constructor
    TXString(const TXString& src);  // Copy constructor
    TXString(TXString&& src);       // Move constructor
    TXString(const StdUStr& src);   // Copy constructor from StdUStr
    TXString(StdUStr&& src);        // Move constructor from StdUStr

    // From other string type
    TXString(const std::string& src, ETXEncoding e = ETXEncoding::eUTF8);
    TXString(const std::wstring& src);
#if GS_MAC
    TXString(const CFStringRef& src);  // Construct from CFStringRef&
#endif

    // Specify initial size
    explicit TXString(size_t nInitialSize);  // Specify size (size_t version)
    explicit TXString(int nInitialSize);     // Specify size (int version)

    // From a buffer
    TXString(const char* src, ETXEncoding e = ETXEncoding::eUTF8);
    TXString(const char* src, size_t len, ETXEncoding e = ETXEncoding::eUTF8);

    TXString(const unsigned char* src, ETXEncoding e = ETXEncoding::eUTF8);

    TXString(const UCChar* src);
    TXString(const UCChar* src, size_t len);

    TXString(const wchar_t* src);
    TXString(const wchar_t* src, size_t len);
    
    // From a character
    explicit TXString(char ch, size_t count = 1);
    explicit TXString(unsigned char ch, size_t count = 1);
    explicit TXString(UCChar ch, size_t count = 1);
    explicit TXString(wchar_t ch, size_t count = 1);
    
    // Destructor
    virtual ~TXString();
```

### Assignment Operators

```cpp
    TXString& operator=(const TXString& src);       // Copy assignment operator
    TXString& operator=(TXString&& src);            // Move assignment operator
    TXString& operator=(const std::string& src);    // Assignment from std::string
    TXString& operator=(const std::wstring&src);    // Assignment from std::wstring
#if GS_MAC
    TXString& operator=(const CFStringRef& src);    // Assignment from CFStringRef&
#endif
    
    // From a buffer
    TXString& operator=(const char *src);
    TXString& operator=(const unsigned char* src);
    TXString& operator=(const UCChar* src);
    TXString& operator=(const wchar_t* src);
    
    // From single character
    TXString& operator=(char ch);
    TXString& operator=(unsigned char ch);
    TXString& operator=(UCChar ch);
    TXString& operator=(wchar_t ch);
    TXString& operator=(int codePoint);        // For example: txString = 0x27
```

### Length Related Functions

```cpp
    size_t GetLength() const;
    size_t GetByteSize() const;
    size_t GetEncodingLength(ETXEncoding e) const;
    
    bool IsEmpty() const;
    
    TXString& SetLength(size_t len);
    TXString& SetLength(size_t len, TXChar ch);
    TXString& Truncate(size_t len);
    TXString& Clear();
```

### Element Access

```cpp
    TXChar& operator[](ptrdiff_t nIndex);
    TXChar operator[](ptrdiff_t nIndex) const;
    TXChar& GetAt(size_t nIndex);
    TXChar GetAt(size_t nIndex) const;
    TXChar& GetLast();
    TXChar GetLast() const;
    TXString& SetAt(size_t nIndex, TXChar ch);
```

### Concatenation

```cpp
    // Extends string from another TXString object
    TXString& operator+=(const TXString& src);
    
    // Extends string from character buffer
    TXString& operator+=(const char* src);           // UTF-8
    TXString& operator+=(const unsigned char* src);  // UTF-8
    TXString& operator+=(const UCChar* src);
    TXString& operator+=(const wchar_t* src);
    
    // Extends string by one character
    TXString& operator+=(char ch);
    TXString& operator+=(unsigned char ch);
    TXString& operator+=(UCChar ch);
    TXString& operator+=(wchar_t ch);
    TXString& operator+=(int n);  // Unicode code point, for example: 0x63
    
    // String concatenation by using '<<'
    TXString& operator<<(const TXString& src);
    
    // Extends string from character buffer
    TXString& operator<<(const char *src);            // UTF-8
    TXString& operator<<(const unsigned char* src);   // UTF-8
    TXString& operator<<(const UCChar* src);
    TXString& operator<<(const wchar_t* src);
    
    // Extends string by one character
    TXString& operator<<(char ch);
    TXString& operator<<(unsigned char ch);
    TXString& operator<<(UCChar ch);
    TXString& operator<<(wchar_t ch);
    
    // Append a number as string
    TXString& operator<<(Sint32 number);
    TXString& operator<<(Sint64 number);
    TXString& operator<<(Uint32 number);
    TXString& operator<<(Uint64 number);
    TXString& operator<<(double number);
#if GS_MAC
    TXString& operator<<(size_t number);     // size_t is different from Uint64 on the mac
#endif
    TXString& operator<<(const GSHandle h);  // as number
```

### Insertion and Deletion

```cpp
    // Insert another TXString at position 'pos'.
    TXString& Insert(size_t pos, const TXString& src);
    
    // Insert one buffer at position 'pos'.
    TXString& Insert(size_t pos, const char* src);
    TXString& Insert(size_t pos, const unsigned char* src);  // UTF-8
    TXString& Insert(size_t pos, const UCChar* src);
    TXString& Insert(size_t pos, const wchar_t* src);
    
    // Insert one character 'ch' at position 'pos'.
    TXString& Insert(size_t pos, char ch);
    TXString& Insert(size_t pos, unsigned char ch);
    TXString& Insert(size_t pos, UCChar ch);
    TXString& Insert(size_t pos, wchar_t ch);
    TXString& Insert(size_t pos, int ch);                    // e.g. 0xA5, '¥'
    
    // Delete characters starting from 'pos' for 'len' characters.
    TXString& Delete(size_t pos, size_t len = (size_t) -1);
    
    // Delete the last character
    TXString& DeleteLast();
    
    // Trims white spaces (' ', '\t') or invalid chars
    TXString& TrimLeft();
    TXString& TrimRight();
    TXString& Trim();

    TXString& TrimLeftInvalidCharacter();
    TXString& TrimRightInvalidCharacter();
    TXString& TrimInvalidCharacters();
```

### Replacement, Conversion, Reversion

```cpp
    // Replaces all 'oldStr' with 'newSTr'.
    TXString& Replace(const TXString& oldStr, const TXString& newStr, bool bIgnoreCase = false);
    
    // Upper case and lower case conversion
    TXString& MakeUpper();
    TXString& MakeLower();
    
    // Reverse the string.
    TXString& MakeReverse();
    
    // Conversion to a Unicode normalization form
    TXString& ToPrecomposed();
    TXString& ToDecomposed();
```

### Getting Data and Casting

```cpp
    // Returns const pointer to the TXChar buffer.
    const TXChar* GetData() const;
    const TXChar* GetTXCharPtr() const;
    
    // Casting operators
    operator const char*() const;           // UTF-8
    operator const unsigned char*() const;  // UTF-8
    operator const UCChar*() const;         // Unsigned short, UTF-16
    operator const wchar_t*() const;        // wchar_t: Win:UTF-16, Mac:UTF-32
    
    // Casting operators in function form.
    const char* GetCharPtr() const { return (operator const char*()); }
    const unsigned char* GetUCharPtr() const { return (operator const unsigned char*()); }
    const UCChar* GetUCCharPtr() const { return (operator const UCChar*()); }
    const wchar_t* GetWCharPtr() const { return (operator const wchar_t*()); }
    
    // Returns a std string or wstring
    std::string GetStdString(ETXEncoding e = ETXEncoding::eUTF8) const;
    std::wstring GetStdWString() const;
#if GS_MAC
    CFStringRef GetCFStringRef() const;
#endif
```

### Copying Data into External Buffer

```cpp
    // Note: 'bufSize' is the size of the buffer in bytes. For example, "xyz" needs four
    // bytes to include '\0' at the end.

    void CopyInto(char* dst,
                  size_t bufSize,
                  ETXEncoding e = ETXEncoding::eUTF8) const;

    void CopyInto(unsigned char* ps,
                  size_t bufSize,
                  ETXEncoding e = ETXEncoding::eUTF8) const;
    
    void CopyInto(UCChar* dst, size_t bufElemSize) const;

    void CopyInto(wchar_t* dst, size_t bufElemSize) const;
```

### Searching Functions

```cpp
    ptrdiff_t Find(const TXString &subStr, size_t posFirst = 0, bool bIgnoreCase = false) const;
    ptrdiff_t Find(int ch, size_t posFirst = 0, bool bIgnoreCase = false) const;
    ptrdiff_t Find(char ch, size_t posFirst = 0, bool bIgnoreCase = false) const;
    
    ptrdiff_t ReverseFind(const TXString &subStr, size_t posLast = -1, bool bIgnoreCase = false) const;
    ptrdiff_t ReverseFind(int ch, size_t posLast = -1, bool bIgnoreCase = false) const;
    ptrdiff_t ReverseFind(char ch, size_t posLast = -1, bool bIgnoreCase = false) const;
    
    ptrdiff_t FindOneOf(const TXString &charSet, size_t posFirst = 0) const;
    ptrdiff_t ReverseFindOneOf(const TXString &charSet, size_t posLast = -1) const;
    
    ptrdiff_t FindNotOneOf(const TXString &charSet, size_t posFirst = 0) const;
    ptrdiff_t ReverseFindNotOneOf(const TXString &charSet, size_t posLast = -1) const;
```

### Surrogate Functions

```cpp
    bool SurrogatePairAt(size_t nIndex) const;
    bool HasSurrogatePair() const;
```

### Creating Substring

```cpp
    TXString Mid(size_t nFirst, size_t len = -1) const;
    TXString Left(size_t len) const;
    TXString Right(size_t len) const;
```

### Conversion to and from Numerics

```cpp
    Sint32 atoi() const;
    Sint64 atoi64() const;
    Real64 atof() const;
    TXString& itoa(Sint64 value);
    TXString& ftoa(Real64 value);
    TXString& ftoa(Real64 value, Sint32 precision);
    static TXString ToStringInt(Sint64 value);
    static TXString ToStringInt(Sint64 value, Sint32 width, wchar_t paddingChar);
    static TXString ToStringReal(Real64 value);
    static TXString ToStringReal(Real64 value, Sint32 precision, bool fixed=true);
    template<typename T>
    static TXString ToString(T value);
    template<typename T>
    static TXString ToStringHex(T value, bool upperCase = true, size_t symbolsCnt = size_t(-1));
```

### Checking TXChar Types

```cpp
    static bool IsPunctuation(TXChar aTXChar);
    static bool IsWhitespace(TXChar aTXChar);
```

### Comparison Methods

```cpp
    // Equality functions
    bool Equal(const TXString &str) const;
    bool EqualNoCase(const TXString &str) const;
    
    // Comparison functions
    Sint32 Compare(const TXString &str) const;
    Sint32 CompareNoCase(const TXString &str) const;
```

### Formatting

`TXString::Format` and `static TXString::Formatted` can be used to generate formatted strings. These functions require known types to be used:
- short, unsigned short
- int, unsigned int
- long, unsigned long
- long long, unsigned long long
- double
- const char
- const char* (UTF-8 encoded string)
- const wchar_t
- const wchar_t* (UTF-16 on Windows, UTF-32 on Mac)
- const void* (generic pointer)
- TXString

They support the `%` markers: `d`, `i`, `u`, `o`, `x`, `X`, `f`, `F`, `e`, `E`, `g`, `G`, `a`, `A`, `c`, `s`, `p`, `n`.

### Related Non-member Functions

```cpp
// Comparison operators
bool operator==(const TXString& lhs, const TXString& rhs);
bool operator==(const TXString& lhs, const char* rhs);
bool operator==(const char* lhs, const TXString& rhs);
bool operator==(const TXString& lhs, const TXChar* rhs);
bool operator==(const TXChar* lhs, const TXString& rhs);

bool operator!=(const TXString& lhs, const TXString& rhs);
bool operator!=(const TXString& lhs, const char* rhs);
bool operator!=(const char* lhs, const TXString& rhs);
bool operator!=(const TXString& lhs, const TXChar* rhs);
bool operator!=(const TXChar* lhs, const TXString& rhs);

bool operator<=(const TXString& lhs, const TXString& rhs);
bool operator>=(const TXString& lhs, const TXString& rhs);

bool operator<(const TXString& lhs, const TXString& rhs);
bool operator>(const TXString& lhs, const TXString& rhs);

// Plus operator
TXString operator+(const TXString& lhs, const TXString& rhs);
TXString operator+(const TXString& lhs, TXChar rhs);
TXString operator+(TXChar lhs, const TXString& rhs);
TXString operator+(const TXString& lhs, const TXChar* rhs);
TXString operator+(const TXChar* lhs, const TXString& rhs);

// Non-member swap function
void swap(TXString& lhs, TXString& rhs);
```

## Iterators

### TConstForwardIterator

```cpp
class TConstForwardIterator
{
public:
	TConstForwardIterator(const TXString &str, int nStart=0);
	TConstForwardIterator& operator++();
	TConstForwardIterator& operator--();
	operator bool() const;
	bool operator==(char ch) const;
	int GetPosition() const;
	char GetChar() const;
	char GetNextChar() const;
	char GetPrevChar() const;
};
```

### TConstReverseIterator

```cpp
class TConstReverseIterator
{
public:
	TConstReverseIterator(const TXString &str);
	TConstReverseIterator& operator++();
	TConstReverseIterator& operator--();
	operator bool() const;
	bool operator==(char ch) const;
	int GetPosition() const;
	char GetChar() const;
	char GetNextChar() const;
	char GetPrevChar() const;
};
```

### TForwardIterator

```cpp
class TForwardIterator : public TConstForwardIterator
{
public:
	TForwardIterator(const TXString &str, int nStart=0);
	TXString& operator*() const;
	TXString* operator->() const;
};
```

### TReverseIterator

```cpp
class TReverseIterator : public TConstReverseIterator
{
public:
	TReverseIterator(const TXString &str);
	TXString& operator*() const;	
	TXString* operator->() const;
};
```

## Displaying String Content in Xcode Debug Area

TXString uses a C++ STL container to store string content. Xcode displays the container information in a way that is difficult to read the string content. Since the LLDB debugger provides the entire API as Python functions ([LLDB Data Formatters](https://lldb.llvm.org/varformats.html)), we can use this feature to display string content in a clear format. In this example, two files are created in the user folder (`/users/yourname/`). One is `.lldbinit` and the other is `lldb_vectorworks.py`.

### .lldbinit

```python
command script import ~/lldb_vectorworks.py
```

### lldb_vectorworks.py

```python
#=========================================================================================
# lldb_vectorworks.py
# The following code is for displaying TXString content in Xcode debug area.

#=========================================================================================
import sys
import lldb
import lldb.formatters.Logger

CATEGORY_NAME = "vectorworks"

#=========================================================================================
# For displaying StdUStr summary in Xcode
def StdUStrSummary(valobj,internal_dict):
	middle = valobj.GetChildAtIndex(0).GetChildAtIndex(0).GetChildAtIndex(0).GetChildAtIndex(0)
	__s = middle.GetChildAtIndex(1)
	modeAndSize = __s.GetChildAtIndex(0).GetChildAtIndex(0).GetValueAsUnsigned(0)
	
	# String to be returned later.
	retStr = u'u"'
	
	if (modeAndSize & 1) == 0:  # Short/static form
		size = (modeAndSize >> 1)
		
		# Current max array size is 11. If size is greater than 11, this object is not 
		# initialized and the content should be empty.
		if size < 12:	
			txCharBuf = __s.GetChildAtIndex(1).GetPointeeData(0, size).uint16
		
			for i in range(size):
				if txCharBuf[i] == 0:
					break;
				retStr = retStr + unichr(txCharBuf[i])
			
	else:  # Long/dynamic form
		__l = middle.GetChildAtIndex(0)
		
		# We only show the first 1000 characters.
		size = __l.GetChildAtIndex(1).GetValueAsUnsigned(0)
		if size > 1000:
			size = 1000
		txCharBuf = __l.GetChildAtIndex(2).GetPointeeData(0, size).uint16
		
		for i in range(size):
			if txCharBuf[i] == 0:
				break;
			retStr = retStr + unichr(txCharBuf[i])
			
	retStr = retStr + u'"'
	
	return retStr.encode('utf-8')

#=========================================================================================
# For displaying TXString summary in Xcode
def TXStringSummary(valobj,internal_dict):
	return StdUStrSummary(valobj.GetChildMemberWithName('stdUStr'), internal_dict)

#=========================================================================================
def __lldb_init_module(debugger, dict):
	reload(sys)
	sys.setdefaultencoding('utf-8')

	# StdUStr and TXString
	debugger.HandleCommand('type summary add -F lldb_vectorworks.StdUStrSummary StdUStr --category %s' % CATEGORY_NAME)
	debugger.HandleCommand('type summary add -F lldb_vectorworks.TXStringSummary TXString --category %s' % CATEGORY_NAME)

	# Enable the vectorworks category
	debugger.HandleCommand("type category enable %s" % CATEGORY_NAME)
	
#=========================================================================================
```

## See Also

- [Vectorworks 2018 Unicode](./Vectorworks%202018%20Unicode.md)
