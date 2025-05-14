# What is this?

VectorWorks SDK provides predefined types for types. A plug-in must use those types to ensure correct communications with the SDK functions.

**Note:** There are other types defined in the SDK, but the ones described in this article should be used since the others are declared obsolete.

## Types

### Standard C++ Types

| Type Name | Description |
|-----------|-------------|
| `size_t`  | A type suitable for representing the amount of memory. This is an unsigned type. |
| `bool`    | Boolean value type. |
| `float`   | Real 32-bit number. |
| `double`  | Real 64-bit number. |

### Generic Types

| Type Name | Description |
|-----------|-------------|
| `Uint8`   | Unsigned 8-bit number |
| `Sint8`   | Signed 8-bit number |
| `Uint16`  | Unsigned 16-bit number |
| `Sint16`  | Signed 16-bit number |
| `Uint32`  | Unsigned 32-bit number |
| `Sint32`  | Signed 32-bit number |
| `Uint64`  | Unsigned 64-bit number |
| `Sint64`  | Signed 64-bit number |
| `Real32`  | Real 32-bit number |
| `Real64`  | Real 64-bit number |

### Range Constants

| Constant Name      | Constant Value          |
|--------------------|-------------------------|
| `kGSMaxUint8`      | 255                     |
| `kGSMaxUint16`     | 65535                   |
| `kGSMaxUint32`     | 4294967295UL            |
| `kGSMaxUint64`     | 18446744073709551615ULL |
| `kGSMaxSint8`      | 127                     |
| `kGSMaxSint16`     | 32767                   |
| `kGSMaxSint32`     | 2147483647L             |
| `kGSMaxSint64`     | 9223372036854775807LL   |
| `kGSMinUint8`      | 0                       |
| `kGSMinUint16`     | 0                       |
| `kGSMinUint32`     | 0UL                     |
| `kGSMinUint64`     | 0ULL                    |
| `kGSMinSint8`      | -128                    |
| `kGSMinSint16`     | -32768                  |
| `kGSMinSint32`     | -2147483648             |
| `kGSMinSint64`     | -9223372036854775808LL  |

### Strings

There is only one string class [`TXString`](Type%20TXString.md).

[`TXStringArray`](Type%20TXStringArray.md) defines an array of strings.

### SDK Specific

| Type Name                          | Description                                                                 |
|------------------------------------|-----------------------------------------------------------------------------|
| [`MCObjectHandle`](Type%20MCObjectHandle.md) | Identifier of a VectorWorks object. Used to recognize object instances.     |
| `OSType`                           | A 32-bit identifier encoded as a Four Character Code (e.g., `'ApfT'`).      |
| `InternalIndex`                    | Represents an index to an object. This index is document-dependent.         |
| `InternalName`                     | Represents the name of an object. This name is document-dependent.          |
| `ColorRef`                         | Color reference. Identifier of a color in the active color palette.         |
| `OpacityRef`                       | Opacity reference. Identifier of opacity.                                   |
| `WorldCoord`                       | Real number representing world coordinates in a VectorWorks document. WorldCoords are always in units—millimeters. |
| `WorldArea`                        | Represents world coordinate area in a VectorWorks document. (`WorldCoord * WorldCoord`) |
| `WorldVolume`                      | Represents world coordinate volume in a VectorWorks document. (`WorldCoord * WorldCoord * WorldCoord`) |
| [`WorldPt`](Type%20WorldPt.md)           | 2D point in the world coordinate system of the document. Defined by `(x, y)` WorldCoord values. |
| [`WorldPt3`](Type%20WorldPt3.md)         | 3D point in the world coordinate system of the document. Defined by `(x, y, z)` WorldCoord values. |
| [`TransformMatrix`](Type%20TransformMatrix.md) | Defines a world coordinate transformation in the document.                  |

## CallBackPtr

This is an obsolete type. It represents a VectorWorks callback pointer used with obsolete functions Obsolete Function.

The new callback mechanism involves [VCOM (VectorWorks Component Object Model)](VCOM%20(Vectorworks%20Component%20Object%20Model).md) with the interface `ISDK`.

## WorldCoord Comparison

### Magnitude-based WorldCoord Comparison Functions

All WorldCoord comparisons must use these functions.

These WorldCoord comparison functions are necessary because most floating-point numbers that are not whole numbers cannot be represented exactly (they have floating-point approximations).

Double-precision floating-point numbers (`double`) are accurate to 15 digits, but we'll consider WorldCoords equal if they are the same up to the 13th significant digit (to allow for drift).

Note that these functions do and must take the magnitude of the values into account. For example:
- `100,000,000,000,000` must be considered equal to `100,000,000,000,001`.
- `1.00000000000000` must be considered equal to `1.00000000000001`.

### WorldCoordsAreNearlyEqual

```cpp
Boolean WorldCoordsAreNearlyEqual(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoordIsNearlyZero

```cpp
Boolean WorldCoordIsNearlyZero(WorldCoord n);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoordsAreNotNearlyEqual

```cpp
Boolean WorldCoordsAreNotNearlyEqual(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoord1_GE_WorldCoord2

```cpp
Boolean WorldCoord1_GE_WorldCoord2(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoord1_GT_WorldCoord2

```cpp
Boolean WorldCoord1_GT_WorldCoord2(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoord1_LE_WorldCoord2

```cpp
Boolean WorldCoord1_LE_WorldCoord2(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoord1_LT_WorldCoord2

```cpp
Boolean WorldCoord1_LT_WorldCoord2(WorldCoord n1, WorldCoord n2);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoordBumpedHigher

```cpp
WorldCoord WorldCoordBumpedHigher(WorldCoord n);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoordBumpedLower

```cpp
WorldCoord WorldCoordBumpedLower(WorldCoord n);
```

Using epsilon (`kNearlyEqualEpsilonForWorldCoords = 1e-13`) for comparison.

### WorldCoordsAreRelativelyClose

```cpp
Boolean WorldCoordsAreRelativelyClose(WorldCoord n1, WorldCoord n2, double ratio = 1e-7);
