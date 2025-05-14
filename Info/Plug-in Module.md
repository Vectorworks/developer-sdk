Vectorworks (http://www.vectorworks.net) provides an open architecture that allows developers to supplement or replace existing Vectorworks functionality. From the user’s perspective, these new tools, menu commands, and objects are indistinguishable from those built into Vectorworks. As such, they are first-class solutions for the user.

## The Plug-in Module Environment

A plug-in module is a [VCOM (Vectorworks Component Object Model) provider](VCOM%20(Vectorworks%20Component%20Object%20Model).md).

The plug-in module has a single entry point:

Providers of interfaces can be **only** Vectorworks module plug-ins. A module plug-in defines interface implementation using the template function **REGISTER_Interface**:

```cpp
extern "C" long GS_EXTERNAL_ENTRY plugin_main(long action, void* pInfo, long& ioData, CallBackPtr cbp)
{
    // initialize VCOM mechanism
    ::GS_InitializeVCOM( cbp );

    long  reply = 0L;

    // register interface implementations using REGISTER_Interface
    // ...
    // register extension implementations using REGISTER_Extension
    // ...

    return reply;
}
```

## Extensions

The main function initializes VCOM (and the old GS_* function callback `gCBP`) and registers VCOM interface implementations or extension implementations.

An extension is a VCOM implementation of the interface **IExtension** and it provides defining information back to Vectorworks. The extension also provides sink implementation (**IEventSink**) that will receive the events sent from Vectorworks to the extension.

There are four types of extensions currently supported by Vectorworks plug-in modules:

| Extension                              | VCOM Interface                                                                 | SDK Base Class                                      | SDK Main Event Sink                              |
|----------------------------------------|-------------------------------------------------------------------------------|----------------------------------------------------|-------------------------------------------------|
| [Menu Extension](SDK-Menu-General-Info.md)                           | **IExtensionMenu**                | **VWExtensionMenu** | **VWMenu_EventSink** |
| [Parametric Extension](SDK-Parametric-General-Info.md)               | **IExtensionParametric**    | **VWExtensionParametric** | **VWParametric_EventSink** |
| [Tool Extension](SDK-Tool-General-Info.md)                           | **IExtensionTool**                | **VWExtensionTool** | **VWTool_EventSink** |
| [VectorScript functions extension](SDK-VS-Functions-General-Info.md) | **IExtensionVSFunctions** | **VWExtensionVSFunctions** | **VWVSFunctions_EventSink** |

## Resources

Every Vectorworks SDK plug-in has a resource file that provides localizable contents.

- On Mac, the resource file is built into the executable bundle.
- On Windows, the resource file is a separate file located in the same place where the plug-in module file is. The resource file has the same name as the plug-in module file, only with the extension `.vwr`.


## Using Module Plug-ins

More detailed information on using module plug-ins can be found on the page [Using the SDK](Using%20the%20SDK.md).

## See also

[VCOM (VectorWorks Component Object Model)](VCOM%20(Vectorworks%20Component%20Object%20Model).md) | 
[SDK Examples Repository](https://github.com/VectorworksDeveloper/SDKExamples)
