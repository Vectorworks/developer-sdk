By [Vladislav Stanev](mailto:vstanev@nemetschek.net)

## What's that

A parametric object is like a container of VectorWorks objects. When rendering the parametric object, VectorWorks will draw the content (the inner objects inside the parametric) of the parametric object.

Hiding details allows the plug-in that regenerates the content of the parametric to control if each inner object should be drawn inside the parametric or not depending on the layer scale or viewport scale in which the parametric exists.

This will allow the plug-in to mark some of the inner objects so they don't appear if the layer scale or viewport scale is less than a specified threshold or appear if the layer scale is above that threshold.

## Rules

The following rules are established for detailing:

Each plug-in (which describes the parametric type) defines one threshold (N) for all parametric instances of that type.

### Flags

- **`ovHideDetail`**: Object is to be hidden when showing details (looking close in the drawing).
- **`ovHideNonDetail`**: Object is to be hidden when showing general (non-details) (looking at the drawing from far away).

If the current layer scale or viewport scale is:

- `<scale> <= N`: `ovHideNonDetail` objects are drawn.
- `<scale> > N`: `ovHideDetail` objects are drawn.

**Note:** Objects without any of these flags attached are always drawn.

## Setting up

To enable "Hide Details" for that parametric type, your parametric plug-in should have **Parametric Extended Properties** enabled.

The parametric extension must set up the extended properties and enable "Hide Details." To do that, you have to set the following extended property: **`kObjXPropCustomHideFactor`**.

**Note:** The extended properties are manipulated through a [VCOM (VectorWorks Component Object Model)](VCOM%20(Vectorworks%20Component%20Object%20Model).md) interface, **`IExtendedProps`**, which is part of the **PluginSupport** category.

The threshold is set as a *double* value. The value specified is treated as a `1:<value>` scale threshold:

- `1:1` → value = `1.0`
- `1:2` → value = `2.0`
- `2:1` → value = `0.5`
- and so on.

**Note:** A value of `0` for the **`kObjXPropCustomHideFactor`** property is an error situation and is considered as `1`.

```cpp
// filepath: d:\Engineering\developer-sdk\Info\Parametric Hide Details.md
EObjectEvent CObjParametricObj_EventSink::OnInitXProperties(CodeRefID objectID)
{
    // obtain the interface for accessing the extended properties
    using namespace VectorWorks::Extension;
    VCOMPtr<IExtendedProps> extProps(IID_ExtendedProps);

    // All parametric objects of this type will have the threshold set here
    extProps->SetObjectProperty(objectID, kObjXPropCustomHideFactor, double(2));

    return kObjectEventNoErr;
}
```

## Create inner objects

When creating inner objects in the parametric during the Recalculate event, see **Extension Class Implementation**.

To demonstrate, create three lines. The first one is normal, the second is marked with **`ovHideDetail`**, and the third one with **`ovHideNonDetail`**.

```cpp
// filepath: d:\Engineering\developer-sdk\Info\Parametric Hide Details.md
EObjectEvent CObjParametricObj_EventSink::Recalculate()
{
    MCObjectHandle hLine1 = ::GS_CreateLine(gCBP, WorldPt(0, 0), WorldPt(0, 100));
    MCObjectHandle hLine2 = ::GS_CreateLine(gCBP, WorldPt(30, 0), WorldPt(30, 100));
    MCObjectHandle hLine3 = ::GS_CreateLine(gCBP, WorldPt(60, 0), WorldPt(60, 100));

    gSDK->SetObjectVariable(hLine2, ovHideDetail, TVariableBlock((Boolean)true));
    gSDK->SetObjectVariable(hLine3, ovHideNonDetail, TVariableBlock((Boolean)true));
}
```

The result object looks like this:

1. When the layer scale is `1:1` (greater than the example's threshold `1:2`):

   - Drawing **`ovHideDetail`** objects. See the rules above.
   - In the example object, the first line is drawn because it doesn't have flags set, and the second line is drawn because it is marked with **`ovHideDetail`**.

   !**ovHideNonDetailSample**

2. When the layer scale is `1:4` (less than the example's threshold `1:2`):

   - Drawing **`ovHideNonDetail`** objects. See the rules above.
   - In the example object, the first line is drawn because it doesn't have flags set, and the third line is drawn because it is marked with **`ovHideNonDetail`**.

   !**ovHideDetailSample**
