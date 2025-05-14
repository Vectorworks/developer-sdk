By [Vladislav Stanev](mailto:vstanev@nemetschek.net)

## What is that?

A symbol break is one type of break that walls can contain. See [Working with Wall Breaks](Working%20with%20Wall%20Breaks.md).

A symbol break specifies the insertion of an instance of a symbol or plug-in object into a wall. The symbol cuts the wall as necessary.

A symbol break defines the symbol definition of the inserted symbol, the height of the symbol’s insertion point in the wall, and the orientation of the symbol (towards which quadrant of the wall the symbol is directed).

The following functions are defined for working with breaks:
- AddBreak -- add a new break to an existing wall.
- SetBreak -- updates break information.
- DeleteBreak -- removes a break from an existing wall.
- ForEachBreak -- enumerates breaks of an existing wall.

The symbol break is defined by the type constant:

## Symbol Break Info

```cpp
const short kSymbolBreak = 1;
```

The symbol break is represented by the structure:

```cpp
struct SymbolBreakType {
  MCObjectHandle  theSymbol;
  Boolean         rightSide;
  Boolean         flipH;
  WorldCoord      height;
						
  InsertModeType  insertMode;
  BreakModeType   breakMode;
};
```

- `theSymbol` -- Handle to the symbol inserted.
- `rightSide` -- Is the symbol oriented to the right side of the wall.
- `flipH` -- Is the symbol flipped toward the first point of the wall.
- `height` -- Height of the bottom of the symbol from the ground plane.
- `insertMode` -- Which edge the symbol is inserted on:
```cpp
const InsertModeType kSymInsertOnCenter    = 0;
const InsertModeType kSymInsertOnLeftEdge  = 1;
const InsertModeType kSymInsertOnRightEdge = 2;
```
- `breakMode` -- Break style for the symbol:
```cpp
const BreakModeType kSymFullBreakWithCaps = 1;
const BreakModeType kSymFullBreakNoCaps   = 2;
const BreakModeType kSymHalfBreak         = 3;
const BreakModeType kSymNoBreak           = 4;
```

## Adding a Break

```cpp
// 1. Create temp parametric object to be inserted into the wall
MCObjectHandle	hObj  = gSDK->CreateCustomObject( TXString("Window"), WorldPt(0,0), 0, false );

// 2. Create symbol break definition
SymbolBreakType	symBreak;
symBreak.theSymbol    = hObj;
symBreak.rightSide    = false;
symBreak.flipH        = false;
symBreak.height       = 10;
symBreak.insertMode   = kSymInsertOnCenter;
symBreak.breakMode    = kSymFullBreakWithCaps;

// 3. Insert the break into the wall
short breakIndex = gSDK->AddBreak( hWall, 100, kSymbolBreak, & symBreak );

// 4. Delete the temp parametric object
gSDK->DeleteObject( hObj, false );

// 5. Reset the wall to apply the new breaks
gSDK->ResetObject( hWall );
```

The object that is passed in as `theSymbol` to **AddBreak** is internally duplicated. This means that the user is responsible for deleting the passed-in handle.

**Note:** Step one will create a "Window" parametric object. If this object is created for the first time in the document, Vectorworks will show the object's default parameters dialog (object preferences). To prevent that from happening, you can define the format prior to the creation of the object:
```cpp
gSDK->DefineCustomObject( TXString("Windows"), kCustomObjectPrefNever );
```

## Editing a Break

```cpp
// Search for the break of interest
gSDK->ForEachBreak( hWall, EnumerateBreaksCallback, NULL );

// Update changes in the wall breaks
gSDK->ResetObject( hWall );
```

Where the enumeration callback is (we want to replace all symbol breaks with doors flipped horizontally):
```cpp
static Boolean EnumerateBreaksCallback(MCObjectHandle hWall, long index, WorldCoord off, long breakType, void* pBreakData, CallBackPtr cbp, void* pMyEnv)
{
  if ( breakType == kSymbolBreak ) {
    SymbolBreakType*	pSymbolBreak	= (SymbolBreakType*) pBreakData;

    // 1. Create temp new object for the break
    MCObjectHandle	hObj	= gSDK->CreateCustomObject( TXString("Door"), WorldPt(0,0), 0, false );

    // 2. Modify the break
    pSymbolBreak->flipH		= true;
    pSymbolBreak->theSymbol	= hObj;

    // 3. Update break information
    gSDK->SetBreak( hWall, index, off, kSymbolBreak, pSymbolBreak );

    // 4. Delete the temp object
    gSDK->DeleteObject( hObj, false );
  }

  return false; // continue
}
```

## Removing a Break

```cpp
gSDK->DeleteBreak( hWall, breakIndex );

// Reset the wall in order for removed breaks to be applied
gSDK->ResetObject( hWall );
```

## See Also

- [Working with Wall Breaks](Working%20with%20Wall%20Breaks.md)
- [Working with Wall Breaks - Cap](Working%20with%20Wall%20Breaks%20-%20Cap.md) | [Working with Wall Breaks - Half](Working%20with%20Wall%20Breaks%20-%20Half.md) | [Working with Wall Breaks - Peak](Working%20with%20Wall%20Breaks%20-%20Peak.md) | [Working with Wall Breaks - Symbol](Working%20with%20Wall%20Breaks%20-%20Symbol.md)
