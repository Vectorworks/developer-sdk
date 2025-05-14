# What is this?

## Definition

```cpp
// constants for the array operators for point types
enum {
	kXIndex = 0,
	kYIndex = 1,
	kZIndex = 2
};

struct WorldCoordBase { 
	_WorldCoord x, y, z;
	WorldCoord&		operator[](int i);
	WorldCoord		operator[](int i);
	Boolean			operator==(const WorldCoordBase& p);
	Boolean			operator!=(const WorldCoordBase& p) const;
};


//---------------------------------------------
struct _WorldPt3 : public WorldCoordBase {
	typedef WorldCoord WorldCoordType;
	
	WorldCoord X() const;
	WorldCoord Y() const;
	WorldCoord Z() const;

	WorldPt3* operator&(void);
	const WorldPt3* operator&(void) const;
};

//---------------------------------------------
struct WorldPt3 : public _WorldPt3 {

	WorldPt3(void);
	
	WorldPt3(const WorldPt3& p);

	WorldPt3(WorldCoord x, WorldCoord y, WorldCoord z);
	WorldPt3(WorldPt p, WorldCoord z);
	WorldPt3(const _WorldPt3& p);

	void Set(WorldCoord x, WorldCoord y, WorldCoord z);
	
	void SetPoint(WorldPt p, WorldCoord z);

	Boolean operator==(const WorldPt3& p) const;
	Boolean operator!=(const WorldPt3& p) const;

	WorldPt3	operator+(const WorldPt3& p) const;
	WorldPt3	operator-(const WorldPt3& p) const;
	WorldPt3	operator-(void) const;

	WorldPt3&	operator=(const WorldPt3& p);
	WorldPt3&	operator+=(const WorldPt3& p);
	WorldPt3&	operator-=(const WorldPt3& p);

	WorldPt3& operator*=(double s);
	WorldPt3& operator/=(double s);
	
	//WorldPt3 GS_API operator*(double_gs scalar) const;
	WorldPt3 GS_API operator/(double scalar) const;

	Boolean IsZero(void) const;
	
	WorldPt3 GS_API Abs(void) const;
	WorldPt3 Half(void) const;

	bool Parallel(const WorldPt3 & p) const;

	// compute the distance from 'this' WorldPt3 to parameter 'p'
	double DistanceFrom(const WorldPt3 & p) const ;

	// It's faster to compute squared distances
	double SquaredDistanceFrom(const WorldPt3 & p) const;

	WorldCoord Magnitude(void)        const;
	WorldCoord MagnitudeSquared(void) const;

	// Perform cross product
	WorldPt3 Cross(const WorldPt3 & p);
	// Perform dot product
	WorldCoord Dot(const WorldPt3 & p);

	void            Normalize(void);
	WorldPt3 GS_API Normal(void) const;
	Boolean         IsNormalized(void) const;

	void GS_API ByteSwap();
};
```

## Function

*To be supplied.*

## Remarks

The default constructor will create an infinite (x, y, z) point — the point will have coordinates `(kMaxLegalWorldCoord, kMaxLegalWorldCoord, kMaxLegalWorldCoord)`.

For more vector representation, there is the type defined:

```cpp
typedef WorldPt3 Vector;
typedef WorldPt3 NormalVector;
typedef WorldPt3 WorldVector;
typedef _WorldPt3 _Vector;
```

## Relative Functions

### `operator*`

```cpp
WorldPt3 operator*(const double scalar, const WorldPt3 thePt);
WorldPt3 operator*(const WorldPt3 inPt, const double inScalar);
```

### `WorldPt3sAreRelativelyClose`

```cpp
Boolean WorldPt3sAreRelativelyClose(WorldPt3 p1, WorldPt3 p2, double ratio = 1e-7);
```

### `CrossProduct`

Calculates the cross product `(p2.z*p1.y - p1.z*p2.y, p2.x*p1.z - p1.x*p2.z, p2.y*p1.x - p1.y*p2.x)` of two points representing 3D vectors.

The cross product magnitude is primarily used for determining the left/right orientation of two vectors.

```cpp
WorldPt3 CrossProduct(const WorldPt3& p1, const WorldPt3& p2);
```

### `DotProduct`

The mathematical definition of Dot Product is:
- `|a||b|cos(theta)` or
- the magnitude of `a` times the magnitude of `b` times the cosine of the angle between `a` and `b`.

The dot product is a general-purpose value that can be used as part of a calculation to:
- Find the angle between two vectors.
- Find the projection of one vector onto another vector.
- Determine the forward/reverse orientation of two vectors.

```cpp
WorldCoord DotProduct(const WorldPt3& p1, const WorldPt3& p2);
```
