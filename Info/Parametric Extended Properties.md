# General Info

See [Parametric General Info](Parametric%20General%20Info.md) for more information on how to set extended properties for a parametric object.

## Extended Properties

### `kObjXPropHasUIOverride`

Allows customization of the shape pane: [Parametric Custom Shape Pane](Parametric%20Custom%20Shape%20Pane.md). The plug-in can specify the widgets that appear in the shape pane when this object is selected.

```cpp
pExtProps->SetObjectProperty(objectID, kObjXPropHasUIOverride, true);
```

### `kObjXHasCustomWidgetVisibilities`

Setting this property to true will cause sending **kObjOnWidgetPrep** event. Using this event will allow the plug-in to update the shape pane according to that particular instance.

See [Parametric Custom Shape Pane](Parametric%20Custom%20Shape%20Pane.md).

```cpp
pExtProps->SetObjectProperty(objectID, kObjXHasCustomWidgetVisibilities, true);
```

### `kObjXHasCustomWidgetValues`

Setting this property to true will cause sending **kObjOnWidgetValueUpdate** event. Using this event will allow the plug-in to update the widgets' data. This is used for widgets that are not bound to record fields but managed manually by the plug-in.

```cpp
pExtProps->SetObjectProperty(objectID, kObjXHasCustomWidgetValues, true);
```

### `kObjXPropAcceptStates`

Do not use that.

```cpp
pExtProps->SetObjectProperty(objectID, kObjXPropAcceptStates, true);
```

### `kObjXPropCustomHideFactor`

The parametric object provides a custom scale threshold to be used to hide inner objects when displaying the object in a layer or VP.

The plug-in provides a custom hide factor as a double number. See [Parametric Hide Details](Parametric%20Hide%20Details.md).

The property value must be a non-zero double number. A zero factor is an error situation and is considered as 1.

```cpp
pExtProps->SetObjectProperty(objectID, kObjXPropCustomHideFactor, double(2));
```

### `kObjXPropHasContextMenu`

Set this property to true if all the instances of that parametric object type have a customized context menu. See: [Parametric Custom Context Menu](Parametric%20Custom%20Context%20Menu.md).

Enabling the context menu will cause sending events **kObjOnContextMenuInit** and **kObjOnContextMenuEvent** that will allow the plug-in to define and update the custom context menu.

```cpp
pExtProps->SetObjectProperty(objectID, kObjXPropHasContextMenu, true);
```

## Not Documented

### `kObjXPropEditGroup`

Object has non-default "Enter Group" behavior. PIO objects can enter the profile or path group directly.

This property is designed to work with *kObjectEditGroupPropertyID* property values below.

Set this using `SetObjectPropertyChar()`, with one of `kEditGroupDefaultProperty`, `kEditGroupProfile`, `kEditGroupPath`, or `kEditGroupCustom`.

`kEditGroupProfile` and `kEditGroupPath` will take you right into editing the path or profile with no dialog. `kEditGroupCustom` brings up the "Enter Path/Profile" selection dialog and lets you set whether a double-click takes you to the path or profile. Note that this requires trapping the `kObjOnSpecialEditID` event as well, and (somehow) finding out what the user set in this dialog.

### `kObjXPropHasLayerScaleDeps`

Object wants to be reset when its layer scale changes.

### `kObjXPropSpecialEdit`

Depends on:

Object has non-default behavior when the "Edit..." command is invoked.

Cursor double-click also invokes this "Edit..." behavior.

One of: `kSpecialEditDefaultProperty`, `kSpecialEditCustom`, `kSpecialEditProperties`, or `kSpecialEditReshape`.

Setting `kSpecialEditCustom` will cause the `kObjOnSpecialEditID` event to be sent when the user double-clicks the parametric object.  
Setting `kSpecialEditProperties` will cause the standard properties dialog for the object (matching the OIP) to display on a double-click.

### `kObjXPropPreference`

Object Definition Procedure (ODP) handles `kParametricPreference` event.

### `kObjXPropDirectModeling`

Object Definition Procedure (ODP) handles Direct Modeling events.

### `kObjXPropAttributeTool`

Object Definition Procedure (ODP) handles Attribute Mapping events.

### `kObjXPropPreventWallInsertion`

Object does NOT want to be inserted into walls. Observed by the 2D cursor tool to prevent dragging into a wall.

### `kObjXPropCustomCursorResize`

NNA under development.

### `kObjXPropAcceptsMarkers`

None.

### `kObjXPropDefaultPropertyUI`

The bits in this property describe the UI displayed.

NNA under development.

### `kObjXHandlesCommand`

NNA under development.

The parametric handles commands.

### `kObjXIs2DSurfaceEligable`

Weiler operations operate on 2D paths.

### `kObjXPropRotatesWithVCS`

Plug-in objects rotate with plan rotation.

### `kObjXPropAcceptStatesInternal`

Plug-in accepts "about to be deleted" state.

The plug-in will receive "about to be deleted" and "duplicate" (internal) states through `kObjXPropAcceptStates`.  
Must have `kObjXPropAcceptStates` enabled.  
Note: This is an isolated option to improve the speed of plug-ins that are not interested in delete events.

### `kObjXPropHasComponents`

The parametric defines components.
