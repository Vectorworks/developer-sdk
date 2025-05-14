# Menu Commands

The SDK supports two types of Menu Commands. A plug-in Menu Command can be either a single basic menu command or a block of several related menu commands, which is referred to as a menu chunk.

A Basic menu command implements a single menu item in a menu. Basic menu commands have no support for item checking, run-time item disabling, or multiple commands in a single plug-in. If you need any of these behaviors you can create a menu chunk.

A menu chunk displays several menu commands together in a menu, and shares the same source code. This is often desirable when the user needs to select between a group of related options that perform a very similar function.

## Register Menu Extension

Registering the extension is done in the module main function. See [Plug-in Module#The Plug-in Module Environment](Plug-in%20Module.md#The%20Plug-in%20Module%20Environment).

This code registers a menu extension implementation class `CExtMenu` as such in the `GROUPID_ExtensionMenu` VCOM grouping (groups are used to improve the performance of Vectorworks when enumerating the available extensions):

```cpp
REGISTER_Extension<TesterModule::CExtMenu>( GROUPID_ExtensionMenu, action, pInfo, ioData, cbp, reply );
```

## Extension Class Definition

```cpp
namespace TesterModule
{
    using namespace VWFC::PluginSupport;

    // ------------------------------------------------------------------------------------------------------
    class CMenu_EventSink : public VWMenu_EventSink
    {
    public:
                            CMenu_EventSink(IVWUnknown* parent);
        virtual             ~CMenu_EventSink();

    public:
        virtual void        DoInterface();
    };

    // ------------------------------------------------------------------------------------------------------
    class CExtMenu : public VWExtensionMenu
    {
        DEFINE_VWExtension;
    public:
                      CExtMenu(CallBackPtr cbp);
        virtual       ~CExtMenu();
    };
}
```

- **Note #1**: The class `CMenu_EventSink` is the implementation of the sink that will receive the events for the menu command. A default menu event sink is the implementation of the class [VWFC:PluginSupport:VWMenu_EventSink](VWFC:PluginSupport:VWMenu_EventSink).
- **Note #2**: The `CExtMenu` is the menu extension definition class. It is a subclass of [VWFC:PluginSupport:VWExtensionMenu](VWFC:PluginSupport:VWExtensionMenu).

## Extension Class Implementation

```cpp
using namespace TesterModule;

namespace TesterModule
{
    // --------------------------------------------------------------------------------------------------------
    static SMenuDef        gMenuDef = {
        /*Needs*/             0x00,
        /*NeedsNot*/          0x00,
        /*Title*/             {12000, 1},
        /*Category*/          {12000, 2},
        /*HelpText*/          {12000},
        /*VersionCreated*/    16,
        /*VersoinModified*/   0,
        /*VersoinRetired*/    0,
        /*ContextualHelpID*/  ""
    };

    static SSTRResource    gMenuChunksDef[] = {
        { {12000, 4}, "" },
        { {12000, 5}, "" },
        { {12000, 6}, "" },
        { {12000, 7}, "" },
        { {12000, 8}, "" },
        { {NULL, NULL}, NULL }
    };
}

// --------------------------------------------------------------------------------------------------------
// {6B6448A9-1DBB-4541-B2D7-862FB517C868}
IMPLEMENT_VWExtension(
    /*Extension class*/  CExtMenu,
    /*Event sink*/       CMenu_EventSink,
    /*Universal name*/   "CExtMenu",
    /*Version*/          1,
    /*UUID*/             0x6b6448a9, 0x1dbb, 0x4541, 0xb2, 0xd7, 0x86, 0x2f, 0xb5, 0x17, 0xc8, 0x68 );

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CExtMenu::CExtMenu(CallBackPtr cbp)
    : VWExtensionMenu( cbp, gMenuDef, NULL )
//  : VWExtensionMenu( cbp, gMenuDef, gMenuChunksDef )
{
}

CExtMenu::~CExtMenu()
{
}

// --------------------------------------------------------------------------------------------------------
// --------------------------------------------------------------------------------------------------------
CMenu_EventSink::CMenu_EventSink(IVWUnknown* parent)
    : VWMenu_EventSink( parent )
{
}

CMenu_EventSink::~CMenu_EventSink()
{
}

void CMenu_EventSink::DoInterface()
{
    gSDK->AlertInform( "hello menu command" );
}
```

- **Note #1**: Implementing [VWFC:PluginSupport:VWExtensionMenu](VWFC:PluginSupport:VWExtensionMenu) requires use of that structure to define the menu extension. Note that the structure contains localizable strings, which are referenced by their resource IDs.
- **Note #2**: If the menu command defined is required to be a chunk of submenus, definition of this structure is needed to define the chunk menu items.
- **Note #3**: A macro that identifies the sink for the extension, the universal name, and the [VCOM:VCOM (Vectorworks Component Object Model)](VCOM:VCOM%20(Vectorworks%20Component%20Object%20Model)) [UUID](VCOM:VWIID).
- **Note #4**: Implementation of the custom menu extension. Use the appropriate constructor depending on whether there are chunk items or not.
- **Note #5**: Implementation of the custom menu event sink.
- **Note #6**: This is the function being called when the menu command is executed by the user.

## SMenuDef

These two fields each contain one word of flags, which determine the default highlighting behavior of the menu item. `Needs` specifies conditions that must exist for the menu item to be available, and `NeedsNot` specifies conditions that must not exist. The meaning of each bit of the two flag fields is identical:

| Bit | Meaning                                   |
|-----|-------------------------------------------|
| 0   | VectorWorks Document is active.           |
| 1   | Reserved                                  |
| 2   | Reserved                                  |
| 3   | Reserved                                  |
| 4   | Reserved                                  |
| 5   | Reserved                                  |
| 6   | Reserved                                  |
| 7   | There are one or more objects selected.   |
| 8   | There is more than one object selected.   |
| 9   | The selection contains at least one 2D object. |
| 10  | The selection contains at least one 3D object. |
| 11  | The current layer is in plan projection.  |
| 12  | Reserved                                  |
| 13  | Reserved                                  |
| 14  | Reserved                                  |
| 15  | Reserved                                  |

If `Needs = 0` and `NeedsNot = 0`, then the item is always active.

If `Needs` and `NeedsNot` both have a corresponding flag set, the item is always inactive. The reserved flags should not be set, and in particular `Needs = 0xFFFF` and `NeedsNot = 0xFFFF` is a reserved setting.
