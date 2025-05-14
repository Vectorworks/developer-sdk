# Parametric Object Types

There are several different types of Parametric Objects. Each type supports different creation and editing features.

All types of Parametric Objects contain a translation matrix that contains the location and rotation angle for that object. The matrix also enables the developer to write the object’s code as if it were inside a local coordinate space (i,j).

For example, if the developer writes code to draw part of the object at (0,0), then that is the origin of the object’s space, not the global space. Most objects won't need to know where in global space they are located, and won’t need to know if they are rotated. This will simplify the developer’s task.

The object types are:

- **Point Parametric Object**
- **Linear Parametric Object**
- **Rectangular Parametric Object**
- **2D Path Parametric Object**
- **3D Path Parametric Object**
- **Custom Parametric Object**

## Point Parametric Object

A Point Parametric Object is typically created by asking the user to click once in the drawing to specify the location point.

A second click can be used to specify the rotation angle of the object. This is similar to the way Symbols are inserted in VectorWorks. Once created, a Point object cannot be reshaped using the cursor tool, only by changing its parameter values on the Object Info palette.

## Linear Parametric Object

A Linear Parametric Object has two points that define its geometry.  
It actually has a matrix and a special length field. The first user-specified point is the origin of the matrix, and the second point is on the i-axis of the matrix at a distance from the origin defined by the length field.

The special length field is actually a parameter named `LineLength` of type `kFieldCoordDisp` or `7`. After a Linear object is created, the user can reshape it using the cursor tool by clicking and dragging either endpoint.

## Rectangular Parametric Object

A Rectangular Parametric Object is similar to a linear object with an additional field.

It has a `LineLength` and `BoxWidth` field that define its shape.

The origin will be at the midpoint of one side of the rectangle, and the i-axis is along the center line.

The rectangle is symmetrical about the i-axis, and therefore half of the object will be drawn with positive “j” values, and half will be drawn with negative "j" values.

A rectangular object will display eight handles when selected. These handles allow the user to graphically reshape the object by dragging them.

## 2D Path Parametric Object

Path Parametric Objects contain a polyline or NURBS curve path object to help define their shape. The object may be reshaped using the reshape tools, or the Object Info palette.

## 3D Path Parametric Object

Path Parametric Objects contain a polyline or NURBS curve path object to help define their shape. The object may be reshaped using the reshape tools, or the Object Info palette.

## See Also

[Parametric General Info](Parametric%20General%20Info.md)
