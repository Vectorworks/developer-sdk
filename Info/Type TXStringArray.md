# TXStringArray

## What is this?

This class represents an array of [`TXString`](Type%20TXString.md). This is an old class, and recent SDKs use the STL **`std::vector`** instead.

## Definition

```cpp
class TXStringArray
{
public:
	TXStringArray(int nInitialSize=0, int nGrowBy=10);
	TXStringArray(const TXStringArray &src);
	~TXStringArray();

	TXStringArray&	operator=(const TXStringArray &src);

	int GetSize() const;
	bool SetSize(Uint32 nSize);

	const TXString& GetAt(Uint32 nIndex) const;
	TXString& GetAt(Uint32 nIndex);
	const TXString& operator[](Uint32 nIndex) const;
	TXString& operator[](Uint32 nIndex);

	void SetAt(Uint32 nIndex, const TXString &string);
	void SetAt(Uint32 nIndex, ConstGSCStrPtr string);
	void SetAt(Uint32 nIndex, ConstGSStringPtr string);

	bool Append(const TXString &string);
	bool Append(ConstGSCStrPtr string);
	bool Append(ConstGSStringPtr string);
	bool Append(const TXStringArray &src);

	bool Append(const PString &string);

	void RemoveAt(Uint32 nIndex, Uint32 nCount = 1);
	void RemoveAll();

	Uint32	FindString (const char* str, bool& bFound);
};
