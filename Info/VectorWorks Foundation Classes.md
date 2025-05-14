# What is this?

VectorWorks Foundation Classes (VWFC) is a library that provides a set of classes for simplifying the development of VectorWorks plug-ins using the SDK.

VWFC is composed of several namespaces containing classes with different purposes.

| Namespace | Description |
|-----------|-------------|
| **VWFC:Math** | Contains classes for mathematical operations. A plug-in normally uses those classes to do calculations and prepare the data for creating VectorWorks objects. |
| **VWFC:VWObjects** | Contains wrapper classes for [MCObjectHandle](Type%20MCObjectHandle.md). The plug-in uses [MCObjectHandle](Type%20MCObjectHandle.md) to pass around when creating VectorWorks objects. When the plug-in needs to work with the particular object that the [MCObjectHandle](Type%20MCObjectHandle.md) identifies, it creates a proper wrapper class from that namespace and works with the handle through the wrapper class instance. |
| **VWFC:PluginSupport** | Provides classes for different types of plug-ins. This namespace provides a layer above the main function of the plug-in, wrapping it around an abstract class. The plug-in implements the abstract class and receives events through virtual functions. |
| **VWFC:Memory** | Handles memory management for the VWFC plug-ins. Enables tracking of memory leaks in the plug-ins. |
| **VWFC:Tools** | Provides multi-purpose tool classes for different solutions. |

VWFC provides wrapper classes that work with the SDK anyway. So, if you use a VWFC plug-in, you can still use all the **VectorWorks:ISDK** functions.

The resources of a VWFC plug-in are the same as any normal SDK plug-in.

## Setting up for VWFC

Every VectorWorks Plug-in project can use VWFC. The library is provided inside the SDK as a set of header files and a library.

### Set up compiler

Your project settings must include the header search path to the `SDKLib\Include\VWFC` SDK folder.

### Set up linking

Next, you should add the `VWFC.lib` file in your Windows project and the `libVWFC.a` library file in your Mac project.

The library files are located in the SDK along with the rest of the library files for the VectorWorks SDK.

### Set up prefix

In your project's prefix file, you have to define the type of the VWFC plug-in that you will use with the VWFC framework.

Then, you have to include the VWFC framework main file to include the entire VWFC framework classes.

```cpp
// -----------------------------------
// VWFC Framework
#define VWFC_APPLICATION_TOOL
#define PLUGIN_NAME "TestTool Tool"

// Include all VWFC classes
#include "VWFCLibrary.h"
```

There are four macros to define the type of the plug-in:

| Macro | Description |
|-------|-------------|
| VWFC_APPLICATION_PARAMETRIC | Define for Object Plug-in |
| VWFC_APPLICATION_MENU_COMMAND | Define for Menu Command Plug-in |
| VWFC_APPLICATION_TOOL | Define for Tool Plug-in |
| VWFC_APPLICATION_LIBRARY | Define for Library Plug-in |

**PLUGIN_NAME** is defined to supply the plug-in name for memory leak reporting and other debug-related reasons. This is not used in any UI or end-user-related reasons. However, it is needed to be defined for successful compilation.

## Creating plug-in

The VWFC approach to plug-ins is by subclassing the application class from the **PluginSupport**) namespace.

Then, register that user application class into the VWFC framework.

After that, every time VectorWorks needs to call the plug-in with an event, an instance of that application will be created, and the appropriate virtual function will be called to handle the event. The client application class can override that virtual function if it is interested in that particular event.

Depending on the type of the plug-in, you should subclass different applications:

- Object Plug-in
- Menu Command Plug-in
- Tool Plug-in
- Library Plug-in
