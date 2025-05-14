# Parametric Custom Opening in Wall

By [Vladislav Stanev](mailto:vstanev@nemetschek.net)

## What's that

When you have a parametric object placed inside a wall, the wall creates an opening using the convex hull polygon in 2D and the convex hull cube in 3D, created by the 2D and 3D geometry of the parametric object.

![Normal Wall Opening](images/normalWallOpening.jpg)

From VectorWorks 2009, parametric objects are capable of customizing the opening that the parametric object will cut into the wall when placed inside.

![Custom Wall Opening](images/customWallOpening.jpg)

The opening is customizable only in 3D.

The opening is created by a boolean operation with the 3D geometry specified from the parametric object and the wall's 3D body.

**NOTE!** The geometry that determines the selection indication is kept inside each instance. The custom opening is definable only in 3D.

## Creating

The plug-in creates geometry that is to be used as opening cutting geometry and puts it into a special group with the call [`SetCustomObjectWallHoleGroup`](https://developer.vectorworks.net/index.php/VCOM:VectorWorks:ISDK::SetCustomObjectWallHoleGroup).

You can pass a handle to any 3D object or a handle to a group object that contains 3D geometry.

```cpp
// filepath: d:\Engineering\developer-sdk\Info\Parametric Custom Opening in Wall.md
case kParametricRecalculate : {

  // -------------------------------------------------------
  // Custom wall opening
  MCObjectHandle	hOpExtrudeBase	= gSDK->CreatePolyshape();
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(-100,-100), vtCorner, 0, false );
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(0,-50), vtCorner, 0, false );
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(100,-100), vtCorner, 0, false );
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(100,100), vtCorner, 0, false );
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(0,50), vtCorner, 0, false );
  gSDK->AddVertex( hOpExtrudeBase, WorldPt(-100,100), vtCorner, 0, false );
  gSDK->SetPolyShapeClose( hOpExtrudeBase, true );

  // create extrude for cutting the wall
  // NOTE: The plug-in must ensure that the cutting 3D geometry
  // goes all the way through the wall.
  MCObjectHandle	hOpExtrude		= gSDK->CreateExtrude( -1000, 1000 );
  gSDK->AddObjectToContainer( hOpExtrudeBase, hOpExtrude );
  gSDK->SetEntityMatrix( hOpExtrude, transfMat );

  // set the cutting geometry
  gSDK->SetCustomObjectWallHoleGroup( objHand, hOpExtrude );
}
```

The way it works is that the plug-in provides 3D geometry that is used to cut through the wall.

![Details Wall Opening](images/detailsWallOpening.jpg)

This way, the plug-in parametric object can control the opening, allowing for recessed and sloped openings. Additionally, if you specify a group containing several 3D bodies, you can create separate independent openings in the wall for this parametric object.
