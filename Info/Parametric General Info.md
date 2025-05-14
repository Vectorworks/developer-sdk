# Parametric Object Extension

This feature provides a way to deliver more powerful and easy-to-use objects. It also allows users to create their own custom objects. A Custom Parametric Object will behave like a built-in object and will draw itself based on the current values of its parameters. Users can easily edit the parameter values using the Object Info palette as they do with all Vectorworks objects.

When developing a Custom Parametric Object, you will need to provide two extensions: a parametric and either [a tool](Tool%20General%20Info.md) or [a menu](Menu%20General%20Info.md). The associated [Tool](Tool%20General%20Info.md) or [Menu](Menu%20General%20Info.md) will provide a user interface for creating the Parametric Object. It may be as simple as a [Tool](Tool%20General%20Info.md) that allows the user to click once in the drawing to place the Parametric Object, or it may be a [Menu Command](Menu%20General%20Info.md) that displays a complex series of dialogs to initialize the Parametric Object before it is created. The Parametric Object’s code will not present a user interface. It will simply draw the object based on the current parameter values. It will be called automatically by Vectorworks whenever the object needs to be regenerated, such as when the user changes a parameter value on the Object Info palette.

## Parametric Object Types

There are several different [Parametric Object Types](Parametric%20Object%20Types.md). Each type supports different creation and editing features.

The object types are:
- [Point Parametric Object](Parametric%20Object%20Types.md#point-parametric-object)
- [Linear Parametric Object](Parametric%20Object%20Types.md#linear-parametric-object)
- [Rectangular Parametric Object](Parametric%20Object%20Types.md#rectangular-parametric-object)
- [2D Path Parametric Object](Parametric%20Object%20Types.md#2d-path-parametric-object)
- [3D Path Parametric Object](Parametric%20Object%20Types.md#3d-path-parametric-object)

## Register Parametric Extension

Registering the extension is done in the module main function. See [The Plug-in Module Environment](Plug-in%20Module.md#the-plug-in-module-environment).

This code registers a parametric extension implementation class `CExtObjParametricObj` in the `GROUPID_ExtensionParametric` VCOM grouping (groups are used to improve the performance of Vectorworks when enumerating the available extensions):

```cpp
REGISTER_Extension<TesterModule::CExtObjParametricObj>(GROUPID_ExtensionParametric, action, pInfo, ioData, cbp, reply);
```

## Extension Class Definition

**Note**: Always keep your extension implementation classes in a namespace!

```cpp
namespace TesterModule
{
    using namespace VWFC::PluginSupport;

    // ------------------------------------------------------------------------------------------------------
    class CObjParametricObj_EventSink : public VWParametric_EventSink
    {
    public:
        CObjParametricObj_EventSink(IVWUnknown* parent);
        virtual ~CObjParametricObj_EventSink();

        // Parametric Object action/events constants 
        virtual EObjectEvent OnInitXProperties(CodeRefID objectID);
        virtual EObjectEvent Recalculate();
    };

    // ------------------------------------------------------------------------------------------------------
    class CExtObjParametricObj : public VWExtensionParametric
    {
        DEFINE_VWExtension;
    public:
        CExtObjParametricObj(CallBackPtr cbp);
        virtual ~CExtObjParametricObj();
    };
}
```

- **Note #1**: The class `CObjParametricObj_EventSink` is the implementation of the sink that will receive the events for the parametric object. A default menu event sink is implemented in the class [VWParametric_EventSink](VWFC:PluginSupport:VWParametric_EventSink.md).
- **Note #2**: The `CExtObjParametricObj` is the parametric extension definition class. It is a subclass of [VWExtensionParametric](VWFC:PluginSupport:VWExtensionParametric.md).

## Extension Class Implementation

```cpp
using namespace TesterModule;

namespace TesterModule
{
    // --------------------------------------------------------------------------------------------------------
    static SParametricDef gParametricDef = {
        /*LocalizedName*/           {11100, 1},
        /*SubType*/                 kParametricSubType_Point,
        /*ResetOnMove*/             false,
        /*ResetOnRotate*/           false,
        /*WallInsertOnEdge*/        false,
        /*WallInsertNoBreak*/       false,
        /*WallInsertHalfBreak*/     false,
        /*WallInsertHideCaps*/      false,
    };

    static SParametricParamChc gArrChoices[] = {
        {128, "Line", {11002, 1}},
        {128, "Arc", {11002, 2}},
        {128, "Bezier", {11002, 3}},
        {128, "None", {11002, 4}},
        {129, "Default Legend", {11002, 5}},
        {NULL, NULL, {0, 0}}
    };

    static SParametricParamDef gArrParameters[] = {
        {"p1", {11001, 1}, "False", "False", kFieldBoolean, 0},
        {"p2", {11001, 2}, "False", "False", kFieldBoolean, 0},
        {"p3", {11001, 3}, "\001@17913,7", "\001@17913,7", kFieldText, 0},
        {"p4", {11001, 4}, "", "", kFieldText, 0},
        {"p5", {11001, 5}, "2\"", "2\"", kFieldCoordDisp, 0},
        {"p6", {11001, 6}, "Auto", "Auto", kFieldPopUp, 128},
        {"p7", {11001, 7}, "Auto", "Auto", kFieldPopUp, 129},
        {NULL, {0, 0}, NULL, NULL, EFieldStyle(0), 0}
    };
}

// --------------------------------------------------------------------------------------------------------
// {D475E572-AD8F-4C0F-A836-4952593664DB}
IMPLEMENT_VWExtension(
    /*Extension class*/   CExtObjParametricObj,
    /*Event sink*/        CObjParametricObj_EventSink,
    /*Universal name*/    "TestObjID",
    /*Version*/           1,
    /*UUID*/              0xd475e572, 0xad8f, 0x4c0f, 0xa8, 0x36, 0x49, 0x52, 0x59, 0x36, 0x64, 0xdb
);

// Test with VectorScript:
// SetSelect(CreateCustomObject('TestObjID',0,0,0));

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CExtObjParametricObj::CExtObjParametricObj(CallBackPtr cbp)
      : VWExtensionParametric(cbp, gParametricDef, gArrParameters)
//    : VWExtensionParametric(cbp, gParametricDef, gArrParameters, gArrChoices)
{
}

CExtObjParametricObj::~CExtObjParametricObj()
{
}

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CObjParametricObj_EventSink::CObjParametricObj_EventSink(IVWUnknown* parent)
    : VWParametric_EventSink(parent)
{
}

CObjParametricObj_EventSink::~CObjParametricObj_EventSink()
{
}

EObjectEvent CObjParametricObj_EventSink::OnInitXProperties(CodeRefID objectID)
{
    using namespace VectorWorks::Extension;
    VCOMPtr<IExtendedProps> extProps(IID_ExtendedProps);

    return kObjectEventNoErr;
}

EObjectEvent CObjParametricObj_EventSink::Recalculate()
{
    VWPoint3D offset(-500, 0, 0);

    VWPolygon3DObj poly;
    poly.AddVertex(offset + VWPoint3D(50, 100, 0));
    poly.AddVertex(offset + VWPoint3D(1000, 0, 0));
    poly.AddVertex(offset + VWPoint3D(1000, 1000, 0));
    poly.AddVertex(offset + VWPoint3D(100, 800, 0));
    poly.SetClosed(true);

    return kObjectEventNoErr;
}
```

- **Note #1**: Implementing [VWExtensionParametric](VWFC:PluginSupport:VWExtensionParametric.md) requires defining the parametric extension structure. This structure contains localizable strings referenced by their resource IDs and defines the [Parametric Object type](Parametric%20Object%20Types.md).
- **Note #2**: Choice popup parameters are defined using a structure. The ID value must be consistent for consecutive values of the same popup.
- **Note #3**: Parameters of the parametric object are defined in a structure. These parameters define the parametric's record format.
- **Note #4**: A macro identifies the sink for the extension, the universal name, and the [UUID](VCOM:VWIID.md).
- **Note #5**: Use the appropriate constructor depending on whether there are popup parameter choices.
- **Note #6**: Implementation of the custom menu event sink.
- **Note #7**: Define the [Parametric Extended Properties](Parametric%20Extended%20Properties.md).
- **Note #8**: The reset function regenerates the Parametric Object's visual appearance.

## Parametric Object Tasks

1. Defining Custom Object Info Palette (OIP):
   - [Custom Shape Pane](Parametric%20Custom%20Shape%20Pane.md)
   - [Custom Shape Pane Popup](Parametric%20Custom%20Shape%20Pane%20Popup.md)
