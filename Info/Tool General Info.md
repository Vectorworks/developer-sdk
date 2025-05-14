# Tool Extensions

The Vectorworks SDK can be used to develop Custom Tools. These tools will be driven by the tool extension implementation, they will not run their own event loop.

This enables Vectorworks to provide some standard behaviors across all tools, for example:

- Auto zoom
- Double-click switching to cursor
- Deleting the most recent segment of a polygon, wall, or other multiple point tool
- Escaping a tool to start over without finishing current actions
- Other view changes during a tool such as scroll bar keys

## Register Tool Extension

Registering the extension is done in the module main function. See [Plug-in Module#The Plug-in Module Environment](Plug-in Module#The Plug-in Module Environment).

This code registers a tool extension implementation class `CExtTool` as such in the `GROUPID_ExtensionTool` VCOM grouping (groups are used to improve the performance of Vectorworks when enumerating the available extensions):

```cpp
REGISTER_Extension<TesterModule::CExtTool>( GROUPID_ExtensionTool, action, pInfo, ioData, cbp, reply );
```

## Extension Class Definition

```cpp
namespace TesterModule
{
    using namespace VWFC::PluginSupport;

    // ------------------------------------------------------------------------------------------------------
    class CTool_EventSink : public VWTool_EventSink
    {
    public:
                    CTool_EventSink(IVWUnknown* parent);
        virtual     ~CTool_EventSink();

    public:
        // Whenever tool needs to draw
        virtual void      Draw(IToolDrawProvider* pDrawProvider);
        // When tool is done getting points
        virtual void      HandleComplete();
    };

    // ------------------------------------------------------------------------------------------------------
    class CExtTool : public VWExtensionTool
    {
        DEFINE_VWExtension;
    public:
                    CExtTool(CallBackPtr cbp);
        virtual     ~CExtTool();

    };
}
```

- **Note #1**: The class `CTool_EventSink` is the implementation of the sink that will receive the events for the tool. A default tool event sink is the implementation of the class [VWFC:PluginSupport:VWTool_EventSink](VWFC:PluginSupport:VWTool_EventSink).
- **Note #2**: The `CExtTool` is the tool extension definition class. It is a subclass of [VWFC:PluginSupport:VWExtensionTool](VWFC:PluginSupport:VWExtensionTool).

## Extension Class Implementation

```cpp
using namespace TesterModule;

namespace TesterModule
{
    // --------------------------------------------------------------------------------------------------------
    static SToolDef        gToolDef = {
        /*ToolType*/                    eExtensionToolType_Normal,
        /*ParametricName*/              "",
        /*PickAndUpdate*/               ToolDef::pickAndUpdate,
        /*NeedScreenPlane*/             ToolDef::doesntNeedScreenPlane,
        /*Need3DProjection*/            ToolDef::doesntNeed3DView,
        /*Use2DCursor*/                 ToolDef::use2DCursor,
        /*ConstrainCursor*/             ToolDef::constrainCursor,
        /*NeedPerspective*/             ToolDef::doesntNeedPerspective,
        /*ShowScreenHints*/             ToolDef::showScreenHints,
        /*NeedsPlanarContext*/          ToolDef::needsPlanarContext,
        /*Reserved1*/                   false,
        /*Reserved2*/                   0,
        /*IconID*/                      11300,
        /*DefaultCursorID*/             0, 
        /*Message*/                     {11300, 3},
        /*WaitMoveDistance*/            0,
        /*ConstraintFlags*/             0,
        /*BarDisplay*/                  kToolBarDisplay_XYClLaZo,
        /*MinimumCompatibleVersion*/    900,
        /*Title*/                       {11300, 1},
        /*Category*/                    {11300, 2},
        /*HelpText*/                    {11300},
        /*VersionCreated*/              16,
        /*VersoinModified*/             0,
        /*VersoinRetired*/              0,
        /*fContextualHelpID*/           ""
    };
}

// --------------------------------------------------------------------------------------------------------
// {34E91E01-64B3-4DF9-8437-6B8874C31273}
IMPLEMENT_VWExtension(
    /*Extension class*/   CExtTool,
    /*Event sink*/        CTool_EventSink,
    /*Universal name*/    "MyCustomTool",
    /*Version*/           1,
    /*UUID*/              0x34e91e01, 0x64b3, 0x4df9, 0x84, 0x37, 0x6b, 0x88, 0x74, 0xc3, 0x12, 0x73 );

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CExtTool::CExtTool(CallBackPtr cbp)
    : VWExtensionTool( cbp, gToolDef )
{
}

CExtTool::~CExtTool()
{
}

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CTool_EventSink::CTool_EventSink(IVWUnknown* parent)
    : VWTool_EventSink( parent )
{
}

CTool_EventSink::~CTool_EventSink()
{
}

void CTool_EventSink::Draw(IToolDrawProvider* pDrawProvider)
{
}

void CTool_EventSink::HandleComplete()
{
    VWPoint3D    pt    = this->GetToolPt3D( 0 );

    VWParametricObj    paramObj( "CExtObj", VWPoint2D(pt.x,pt.y) );
}
```

- **Note #1**: Implementing [VWFC:PluginSupport:VWExtensionTool](VWFC:PluginSupport:VWExtensionTool) requires use of that structure to define the tool extension. Note that the structure contains localizable strings, which are referenced by their resource IDs.
- **Note #2**: A macro identifies the sink for the extension, the universal name, and the [VCOM:VCOM (Vectorworks Component Object Model)](VCOM:VCOM (Vectorworks Component Object Model)) [UUID](VCOM:VWIID).
- **Note #3**: Implementation of the custom tool extension.
- **Note #4**: Implementation of the custom tool event sink.
- **Note #5**: This event is used for the tool to provide custom drawing.
- **Note #6**: This event is used when the tool finishes execution. In this example, it creates a parametric object at the first mouse click.
