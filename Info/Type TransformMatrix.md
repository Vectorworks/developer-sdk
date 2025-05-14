# What is this?

This is a standard 4x4 matrix with the right column vector (0 0 0 1) removed (perspective transformation & w).

It should always be orthogonal (all basis vectors are orthonormal).

## Definition

```cpp
class _TransformMatrix 	
{
public:	
	union {
		_WorldCoord	mat[4][3];
		struct {
			double a00;    double a01;    double a02;
			double a10;    double a11;    double a12;
			double a20;    double a21;    double a22;
			_WorldCoord xOff;   _WorldCoord yOff;   _WorldCoord zOff;
		} v1;
		struct {
			_Vector		i, j, k;
			_WorldPt3	offset;
		} v2;
	};
};

// ----------------------------------
class TransformMatrix : public _TransformMatrix
{
public:
	TransformMatrix();

	TransformMatrix& operator=(const TransformMatrix& tm);
	TransformMatrix(const _TransformMatrix& t);
	
	NormalVector C0() const;	// mat[*][0]
	NormalVector C1() const;	// mat[*][1]
	NormalVector C2() const;	// mat[*][2]

	NormalVector& R0(); // mat[0][*]
	NormalVector& R1(); // mat[1][*]
	NormalVector& R2(); // mat[2][*]
	WorldPt3& P();      // mat[3][*]  rename? XXX_JDW_MISC

	// We might want to assert that the vectors are normalized, but the plan is to make
	// NormalVector do some of that and not just be a typedef.
	void SetRow0(const NormalVector& vec); // mat[0][*]
	void SetRow1(const NormalVector& vec); // mat[1][*]
	void SetRow2(const NormalVector& vec); // mat[1][*]
	
	bool GS_API IsOrthogonal() const;
	bool 		Is2DOrHybrid() const;
	bool GS_API IsIdentity() const;
	
	void GS_API SetToIdentity();
	
	void GS_API ByteSwap();
};
```

## Function

*To be supplied.*

## Remarks

*No remarks provided.*

## Relative Functions

```cpp
typedef short MajorAxisSpec;

const MajorAxisSpec kNoAxis = 0;
const MajorAxisSpec kXAxis = 1;
const MajorAxisSpec kYAxis = 2;
const MajorAxisSpec kZAxis = 3;

//**********************************************************************************
//**************************  TransformXMatrix Routines  ***************************
//**********************************************************************************

void GS_API MatrixToXMatrix(const TransformMatrix &source, TransformXMatrix &dest);
TransformXMatrix MatrixToXMatrix(const TransformMatrix &source);
void GS_API XMatrixToMatrix(const TransformXMatrix &source, TransformMatrix &dest);
TransformMatrix XMatrixToMatrix(const TransformXMatrix &source);
Boolean GS_API XMatrix2Matrix(const TransformXMatrix& source, TransformMatrix& dest);

//**********************************************************************************
//**************************  TransformMatrix Routines  ****************************
//**********************************************************************************

// NEW MATRIX TRANSFORMS 3/3/97
void GS_API PointTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API NonLinearPointTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API InversePointTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API InversePointTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API VectorTransformN(const Vector& a, const TransformMatrix& mat, Vector& b);
void GS_API VectorTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API InverseVectorTransformN(const WorldPt3 &a, const TransformMatrix &mat, WorldPt3 &b);
void GS_API InverseVectorTransformN(const Vector &a, const TransformMatrix &mat, Vector &b);
void GS_API CubeTransformN(const WorldCube& in, const TransformMatrix& mat, WorldCube& out);

inline WorldPt3 operator*(const WorldPt3& inPt, const TransformMatrix& inMat);

void InversePoint2DTransform(const WorldPt &a, const TransformMatrix &mat, WorldPt &b);

inline void Point2DTransform(const WorldPt &a, const TransformMatrix &mat, WorldPt &b);

void InverseVector2DTransform(const WorldPt &a, const TransformMatrix &mat, WorldPt &b);

////////////////////////////////////////////////////////////
inline void Vector2DTransform(const Vector2 &a, const TransformMatrix &mat, Vector2 &b);

void PointTransformTo2D(const WorldPt3 &a, const TransformMatrix &mat, WorldPt &b);

void GS_API IdentityMatrix(TransformMatrix &mat);
const GS_API TransformMatrix& IdentityMatrix();

TransformMatrix GS_API MatrixMultiply(const TransformMatrix &mat1, const TransformMatrix &mat2);

void GS_API MatrixMakeOrthogonal(TransformMatrix &mat);
void GS_API InvertMatrix(const TransformMatrix &source, TransformMatrix &dest);
void GS_API SetAxisRotationMatrix(MajorAxisSpec axis, double_param degrees, TransformMatrix &mat);
void GS_API SetAxisRotationMatrix(MajorAxisSpec axis, double_param degrees, const WorldPt3& center, TransformMatrix &mat);
void GS_API SetVectorRotationMatrix(const Vector &theVec, double_param degrees, TransformMatrix &mat);
void GS_API Set2DAxisFlipMatrix(const MajorAxisSpec axis, const WorldPt& origin, TransformMatrix& tm);
Boolean GS_API MakeVectorsOrthonormal(Vector &aVec, Vector &bVec, Vector &cVec, Boolean first);
void GS_API GetVectorsFromMatrix(const TransformMatrix &mat, Vector &aVec, Vector &bVec, Vector &cVec);
void GS_API AssignVectorsToMatrix(const Vector &aVec, const Vector &bVec, const Vector &cVec, TransformMatrix &mat);
void GS_API CreateMatrixFromAxis(const Axis &axis, TransformMatrix &mat);
void GS_API CreateAxisFromMatrix(const TransformMatrix &mat, Axis &axis);
void GS_API SetMatrixKI(const WorldPt3 &kDir, const WorldPt3 &iDir, TransformMatrix &mat);
void GS_API SetMatrixKJ(const WorldPt3 &kDir, const WorldPt3 &jDir, TransformMatrix &mat);
void GS_API SetMatrixIJ(const WorldPt3 &kDir, const WorldPt3 &iDir, TransformMatrix &mat);
void GS_API TranslateMatrix(TransformMatrix &f, WorldCoord x, WorldCoord y, WorldCoord z);
void GS_API SetMultiAxisSpinMatrix(	TransformMatrix &mat,
					MajorAxisSpec firstAxis,
					MajorAxisSpec secondAxis,
					MajorAxisSpec thirdAxis,
					double_param firstAngleDegrees,
					double_param secondAngleDegrees,
					double_param thirdAngleDegrees,
					const WorldPt3 &center,
					Boolean fromzero);

// This function calculates a transformation matrix (non-affine)
// that transforms the first triangle (inPt1, inPt2, inPt3) into (transfPt1, transfPt2, transfPt3) triangle
bool GS_API CalcTransformFromOneTriToAnother(TransformMatrix& outMatrix,
                    const WorldPt3& inPt1, const WorldPt3& inPt2, const WorldPt3& inPt3,
                    const WorldPt3& transfPt1, const WorldPt3& transfPt2, const WorldPt3& transfPt3);

void GS_API RotateMatrix2D(double_param degrees, const WorldPt& center, TransformMatrix &mat);
```