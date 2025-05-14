# What is this?

This class represents a 2D point in the world coordinates.

## Definition

```cpp
class _WorldPt : public TDebugObject
{
public:
	typedef WorldCoord WorldCoordType;
	_WorldCoord y, x;

	WorldCoord X() const;
	WorldCoord Y() const;
	
	Boolean operator==(const _WorldPt& p) const;
	Boolean operator!=(const _WorldPt& p) const;

	void ByteSwap();
};

// ------------------------------------
class WorldPt : public _WorldPt {
public:

	WorldPt(void);
	WorldPt(WorldCoord x, WorldCoord y);

	WorldPt(const WorldPt& p);
	
	
	WorldPt(const _WorldPt& p);
	
	void Set(WorldCoord x, WorldCoord y);
	void SetAngle(double radians);

	Boolean operator==(const WorldPt& p) const;
	Boolean operator!=(const WorldPt& p) const;

	WorldPt operator+(const WorldPt& p) const;
	WorldPt operator-(const WorldPt& p) const;
	WorldPt	operator*(double scalar) const;
	WorldPt	operator/(double scalar) const;
	
	WorldPt operator-(void) const;
	
	WorldPt& operator=(const WorldPt& p);
	
	WorldPt& operator+=(const WorldPt& p);
	WorldPt& operator-=(const WorldPt& p);
	WorldPt& operator*=(double scalar);
	WorldPt& operator/=(double scalar);

	Boolean IsZero(void) const;

	WorldCoord Magnitude(void);
	WorldCoord MagnitudeSquared(void);
	void       Normalize(void);

	// This function assumes that the WorldPt is a vector. 
	// This poorly named function returns a unit vector, or a normalized vector, NOT a perpendicular vector.
	WorldPt    Normal(void) const;

	// returns a perpendicular 2d vector turning clockwise or counter-clockwise depending on the parameter
	WorldPt Perpendicular(bool clockwise = false) const ;

	bool Parallel(const WorldPt & p);

	// crossing 2d vectors is useful in that it reveals a winding order.
	// This function returns the z value of the cross product.
	WorldCoord CrossMagnitude(const WorldPt & p);

	// Perform dot product
	WorldCoord Dot(const WorldPt & p);
	WorldCoord DistanceFrom(WorldPt otherPoint)        const;
	WorldCoord SquaredDistanceFrom(WorldPt otherPoint) const;

	double AngleInDegrees(void) const;
	double DegreeAngleFrom(const WorldPt inOrigin) const;

	WorldPt Abs(void) const;
	WorldPt Half(void) const;
};
```

## Function

*To be supplied.*

## Remarks

The default constructor of this class will create an infinite (x, y) point -- the point will have coordinates `(kMaxLegalWorldCoord, kMaxLegalWorldCoord)`.

For more vector representation, there is the type defined:

```cpp
typedef WorldPt Vector2;
```

## Relative Functions

### Scalar Multiplication

Multiply operator of a number to the point's coordinates.

```cpp
WorldPt operator*(const double scalar, const WorldPt thePt);
```

### Nearly Equal

Checks if two points are nearly the same according to a ratio (epsilon).

```cpp
Boolean WorldPtsAreRelativelyClose(WorldPt p1, WorldPt p2, double ratio = 1e-7);
```

### Cross Product

Calculates cross product `(a.x * b.y - a.y * b.x)` of two points representing 2D vectors.

Cross product magnitude is primarily used for determining the left/right orientation of two vectors.

```cpp
WorldCoord CrossProductMagnitude(const WorldPt& a, const WorldPt& b);
```

### Dot Product

The mathematical definition of Dot Product is:
- `|a||b|cos(theta)` or 
- the magnitude of `a` times the magnitude of `b` times the cosine of the angle between `a` and `b`.

The dot product is a general-purpose value that can be used as part of a calculation to:
- Find the angle between two vectors.
- Find the projection of one vector onto another vector.
- Determine the forward/reverse orientation of two vectors.

```cpp
WorldCoord DotProduct(const WorldPt& a, const WorldPt& b);
```