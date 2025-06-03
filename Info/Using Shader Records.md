By [Dave Donley](mailto:dave@vectorworks.net)

## Textures

Textures in Vectorworks are stored using records, called shader records. The record values are accessed using RecordHandler routines as for other records in Vectorworks. The first three fields of the shader record are always:

- Family
- Prototype
- Version

**Family values are:**

| Value | Name                |
|-------|---------------------|
| 1     | Color               |
| 2     | Reflectivity        |
| 3     | Transparency        |
| 4     | Bump                |
| 5     | Background          |
| 6     | Foreground          |
| 7     | Sketch (Artistic RW)|

Prototype values depend on the Family value. These are the Prototype values for each Family:

```cpp
enum MaxonColorShaderPrototype {
	kMaxonColor_Plain = 40,
	kMaxonColor_Image = 41,
	kMaxonColor_Fresnel = 42,
	kMaxonColor_Noise = 43,
	kMaxonColor_Tiles = 44,
	kMaxonColor_Bricks = 45,
	kMaxonColor_Pavement = 46
};

enum MaxonReflectivityShaderPrototype {
	kMaxonReflectivity_Image = 30,
	kMaxonReflectivity_Mirror = 31,
	kMaxonReflectivity_Fresnel = 32,
	kMaxonReflectivity_Noise = 33,
	kMaxonReflectivity_Tiles = 34,
	kMaxonReflectivity_Plastic = 35,
	kMaxonReflectivity_Bricks = 36,
	kMaxonReflectivity_Pavement = 37,
	kMaxonReflectivity_Glow = 38,
	kMaxonReflectivity_Backlit = 39,
	kMaxonReflectivity_Anisotropic = 40
};

enum MaxonTransparencyShaderPrototype {
	kMaxonTransparency_Plain = 10,
	kMaxonTransparency_Color = 11,
	kMaxonTransparency_Image = 12,
	kMaxonTransparency_Glass = 13,
	kMaxonTransparency_Noise = 14,
	kMaxonTransparency_Tiles = 15,
	kMaxonTransparency_Bricks = 16,
	kMaxonTransparency_Pavement = 17,
	kMaxonTransparency_RectangularMask = 18,
	kMaxonTransparency_ImageMask = 19
};

enum MaxonBumpShaderPrototype {
	kMaxonBump_Image = 41,
	kMaxonBump_Noise = 42,
	kMaxonBump_Tiles = 43,
	kMaxonBump_Bricks = 44,
	kMaxonBump_Pavement = 45
};
```

Record fields are specific to each shader. Colors are stored as three integer fields in a row, with range of 0..255 and RGB order. For example, the fields for the Color Tiles (**NNAMaxon Tiles Color**) shader are:

| Value | Name                   | Type         |
|-------|------------------------|-------------|
| 1     | Family                 | int         |
| 2     | Prototype              | int         |
| 3     | Version                | int         |
| 4     | Pattern                | int         |
| 5     | Grout Color red        | 0..255 int  |
| 6     | Grout Color green      | 0..255 int  |
| 7     | Grout Color blue       | 0..255 int  |
| 8-10  | Tile Color 1 RGB       | 0..255 int  |
| 11-13 | Tile Color 2 RGB       | 0..255 int  |
| 14-16 | Tile Color 3 RGB       | 0..255 int  |
| 17    | Randomize Color        | boolean     |
| 18    | Grout Width            | 0..1 real   |
| 19    | Bevel Width            | 0..1 real   |
| 20    | Bevel                  | boolean     |
| 21    | Horizontal Orientation | boolean     |
| 22    | Global Scale           | 0..1 real   |
| 23    | Relative Scale U       | 0..1 real   |
| 24    | Relative Scale V       | 0..1 real   |

The fields for the **NNAMaxon Image Bump** shader are:

| Value | Name             | Type       |
|-------|------------------|------------|
| 1     | Family           | int        |
| 2     | Prototype        | int        |
| 3     | Version          | int        |
| 4     | Pattern          | int        |
| 5     | Bump strength %  | 0..1 real  |

The fields for the **NNAMaxon Noise Bump** shader are:

| Value | Name                        | Type       |
|-------|-----------------------------|------------|
| 1     | Family                      | int        |
| 2     | Prototype                   | int        |
| 3     | Version                     | int        |
| 4     | Pattern                     | int        |
| 5     | Bumps Heights 1 %           | 0..1 real  |
| 6     | Bumps Heights 2 %           | 0..1 real  |
| 7     | Option Dimensionality = 3D Solid | boolean |
| 8     | Scale Global %              | 0..1 real  |
| 9     | Scale Relative U %          | 0..1 real  |
| 10    | Scale Relative V %          | 0..1 real  |
| 11    | Scale Relative W %          | 0..1 real  |
| 12    | Option Low Clip %           | 0..1 real  |
| 13    | Option High Clip %          | 0..1 real  |
| 14    | Option Detail               | int        |
| 15    | Option Cycles               | int        |
| 16    | Bumps Strength %            | 0..1 real  |

## Noise type and Tiles patterns

Pattern enumeration values, like for Tile Pattern, match the values shown in the Vectorworks user interface for the shader, and are zero-based. For example, to assign the Hexagons pattern to the shader the pattern enumeration value should be 5.

Enumeration values for Noise type and Tiles pattern are:

```cpp
enum MaxonNoiseType {
	kBox = 0,
	kBlisteredTurbulence = 1,
	kBuya = 2,
	kCellNoise = 3,
	kCranal = 4,
	kDents = 5,
	kDisplacedTurbulence = 6,
	kFBM = 7,
	kHama = 8,
	kLuka = 9,
	kModNoise = 10,
	kNaki = 11,
	kNoise = 12,
	kNutous = 13,
	kOber = 14,
	kPezo = 15,
	kPoxo = 16,
	kRandomNoise = 17,
	kSema = 18,
	kStupl = 19,
	kTurbulence = 20,
	kVLNoise = 21,
	kWavyTurbulence = 22,
	kCellVoronoi = 23,
	kDisplacedVoronoi = 24,
	kSparseConvolution = 25,
	kVoronoi1 = 26,
	kVoronoi2 = 27,
	kVoronoi3 = 28,
	kZada = 29,
	kWoodNoise = 100,
	kMarbleNoise = 101
};


enum MaxonTilesType {
	kBricks1 = 0,
	kBricks2 = 1,
	kCircles1 = 2,
	kCircles2 = 3,
	kCircles3 = 4,
	kHexagons = 5,
	kLines1 = 6,
	kLines2 = 7,
	kParquet = 8,
	kPlanks = 9,
	kRadialLines1 = 10,
	kRadialLines2 = 11,
	kRandomTiles = 12,
	kRings1 = 13,
	kRings2 = 14,
	kSawtooth1 = 15,
	kSawtooth2 = 16,
	kScales1 = 17,
	kScales2 = 18,
	kSpiral1 = 19,
	kSpiral2 = 20,
	kSquares = 21,
	kTriangles1 = 22,
	kTriangles2 = 23,
	kTriangles3 = 24,
	kWaves1 = 25,
	kWaves2 = 26,
	kWeave = 27
};
```

## Image-based Textures

Image textures have record values for anti-aliasing, etc. that are retrieved and modified as with other shader records. The images themselves are stored as ImageDefNodes.

Calling `ISDK::GetObjectVariable` with the following selectors will return the image def node handle for an image-based shader record. Bump, reflectivity, and transparency mask shaders expect to use grayscale images, so those can be retrieved with the grayscale image selector. If none is found then use the non-grayscale selector after trying the grayscale selector first.

```cpp
const short ovShaderRecordImageDefNode = 550;    // MCObjectHandle (image node) - Get only
const short ovShaderRecordGrayscaleImageDefNode = 551; // MCObjectHandle (8-bit image node) - Get only
```

Image def nodes for image-based shaders have TextureBitmap nodes as aux parents. Those can be retrieved and modified through the TexBit routines.

## Shader Record Fields

The following utility routines can be used to easily get and set shader field values. Reminder: Field indices are one-based, first three fields are family, prototype, and version, and all colors use three fields each.

Shader record parameters can be retrieved and/or modified using SDK RecordHandler functions.

```cpp
bool GetShaderRecordFamilyAndPrototype(MCObjectHandle shaderRecord, long& family, long& prototype)
{
	bool result = false;

	family = 0L;
	prototype = 0L;

	if (VERIFYHANDLE(kDaveD, shaderRecord, 1L)) {

		// Get family and prototype fields

		TRecordHandler recordHandler(shaderRecord);
		
		TRecordItem recordItem(kFieldLongInt);

		if (recordHandler.GetFieldObject(1, recordItem) && recordItem.GetFieldValue(family) &&
				recordHandler.GetFieldObject(2, recordItem) && recordItem.GetFieldValue(prototype)) {

			result = true;
		}
	}

	return result;
}


long GetShaderRecordLong(MCObjectHandle shaderRecord, short fieldID)
{
	long result = 0L;

	TRecordHandler recordHandler(shaderRecord);
	TRecordItem recordItem(kFieldLongInt);

	long longValue = 0L;
	if (VERIFYN(kDaveD, recordHandler.GetFieldObject(fieldID, recordItem)) && VERIFYN(kDaveD, recordItem.GetFieldValue(longValue)))
		result = longValue;

	return result;
}


double GetShaderRecordDouble(MCObjectHandle shaderRecord, short fieldID)
{
	double result = 0.0;

	TRecordHandler recordHandler(shaderRecord);
	TRecordItem recordItem(kFieldReal);

	double doubleValue = 0.0;
	if (VERIFYN(kDaveD, recordHandler.GetFieldObject(fieldID, recordItem)) && VERIFYN(kDaveD, recordItem.GetFieldValue(doubleValue)))
		result = doubleValue;

	return result;
}


WorldCoord GetShaderRecordCoord(MCObjectHandle shaderRecord, short fieldID)
{
	WorldCoord result = 0.0;

	TRecordHandler recordHandler(shaderRecord);
	TRecordItem recordItem(kFieldCoordDisp);

	WorldCoord coordValue = 0.0;
	if (VERIFYN(kDaveD, recordHandler.GetFieldObject(fieldID, recordItem)) && VERIFYN(kDaveD, recordItem.GetFieldValue(coordValue)))
		result = coordValue;

	return result;
}


GSColor GetShaderRecordColor(MCObjectHandle shaderRecord, short fieldID)
{
	long redLong = GetShaderRecordLong(shaderRecord, fieldID);
	long greenLong = GetShaderRecordLong(shaderRecord, fieldID + 1);
	long blueLong = GetShaderRecordLong(shaderRecord, fieldID + 2);
	
	GSColor color;
	color.Set8((char) (redLong & 0xff), (char) (greenLong & 0xff), (char) (blueLong & 0xff));

	return color;
}


bool GetShaderRecordBool(MCObjectHandle shaderRecord, short fieldID)
{
	bool result = false;

	TRecordHandler recordHandler(shaderRecord);
	TRecordItem recordItem(kFieldBoolean);

	Boolean boolValue = 0L;
	if (VERIFYN(kDaveD, recordHandler.GetFieldObject(fieldID, recordItem)) && VERIFYN(kDaveD, recordItem.GetFieldValue(boolValue)))
		result = boolValue;

	return result;
}


static void SetShaderRecordLong(MCObjectHandle shaderRecord, short fieldID, long value)
{
	TRecordItem recordItem(kFieldLongInt);
	recordItem.SetFieldValue(value);
	
	TRecordHandler recordHandler(shaderRecord);
	recordHandler.SetFieldObject(fieldID, recordItem);
}


static void SetShaderRecordColor(MCObjectHandle shaderRecord, short fieldID, const GSColor& color)
{
	unsigned char redChar;
	unsigned char greenChar;
	unsigned char blueChar;
	color.Get8(redChar, greenChar, blueChar);

	SetShaderRecordLong(shaderRecord, fieldID, redChar);
	SetShaderRecordLong(shaderRecord, fieldID + 1, greenChar);
	SetShaderRecordLong(shaderRecord, fieldID + 2, blueChar);
}


static void SetShaderRecordDouble(MCObjectHandle shaderRecord, short fieldID, double value)
{
	TRecordItem recordItem(kFieldReal);
	recordItem.SetFieldValue(value);
	
	TRecordHandler recordHandler(shaderRecord);
	recordHandler.SetFieldObject(fieldID, recordItem);
}


static void SetShaderRecordBool(MCObjectHandle shaderRecord, short fieldID, bool value)
{
	TRecordItem recordItem(kFieldBoolean);
	recordItem.SetFieldValue((Boolean) value);
	
	TRecordHandler recordHandler(shaderRecord);
	recordHandler.SetFieldObject(fieldID, recordItem);
}
```

## Making Shader Record Formats Visible

The simplest way to see how parameters are stored for each shader is to create a texture with the desired shader and run the following Vectorscript to make the shader record formats visible:

```pascal
PROCEDURE MakeRecordFormatsVisible;
VAR
temp_h:HANDLE;
temp_i,j:INTEGER;
 
BEGIN
    j := 0;
    FOR temp_i := 1 TO nameNum DO 
    BEGIN
        temp_h := getobject(namelist(temp_i));
        IF (((temp_h <> NIL) & (gettype(temp_h) = 47)) & NOT(getobjectvariableboolean(temp_h,900))) THEN 
        BEGIN
            SetObjectVariableBoolean(temp_h,900,TRUE);
            j := j + 1;
        END;
    END;
END;
run(MakeRecordFormatsVisible);
```

After running this script, any shader record formats used in the document will become visible in the Resource Browser.
