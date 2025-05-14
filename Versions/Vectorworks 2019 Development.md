# General Info

The Vectorworks 2019 SDK requires Visual Studio 2017 (toolset v141) on Windows and Xcode version 8.2 on the Mac.

Vectorworks 2018 has been converted to Unicode using UTF-16 internally. The SDK's TXString class has been converted.

# Dialog Builder

The Dialog Builder tool, used to design Layouts for Script and SDK, is still built-in. [Dialog_Builder](../Info/Dialog%20Builder.md)

# Developer Tools

Developer tools under the 'Debug' menu are available after running the following VectorScript (make a new script in the Resource Manager and run it):

```vs
EnableDebugMenu;
```

Running this script will restart Vectorworks and then a Debug menu will be available.
