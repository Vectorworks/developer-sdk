# The SDK

Vectorworks http://www.vectorworks.net provides an open architecture that allows developers to supplement or replace existing Vectorworks functionality. From the user’s perspective, these new tools, menu commands, and objects are indistinguishable from those built into Vectorworks. As such, they are first-class solutions for the user.

The SDK https://www.vectorworks.net/en-US/support/custom/sdk/sdkdown provides OS-independent functionality for accessing the application routines. Also, it provides functions for creating OS-independent User Interfaces for your plug-ins.

## Basics

The Vectorworks SDK uses the C++ language to develop extension functionality. To develop SDK plug-ins for Vectorworks, you will need the following tools:

Before you begin, you should review [the Vectorworks environment](Info/The%20Vectorworks%20Environment.md).

The Vectorworks SDK provides [some specific types](Info/Types.md). A developer should use the recommended types. Here is a short list of the most commonly used types:
- [MCObjectHandle](Info/Type%20MCObjectHandle.md)
- [TXString](Info/Type%20TXString.md)
- [TXStringArray](Info/Type%20TXStringArray.md)
- [WorldPt](Info/Type%20WorldPt.md)
- [WorldPt3](Info/Type%20WorldPt3.md)
- [TransformMatrix](Info/Type%20TransformMatrix.md)

## Working with the SDK

- [Using the SDK](Info/Using%20the%20SDK.md)
- [Installing Plug-ins](https://github.com/Vectorworks/developer-scripting/blob/main/Common/Partner%20Install/README.md)

## Plug-ins Modules

Vectorworks plug-in is a dynamic library on Windows and a specific type of bundle on Mac.

On launch, Vectorworks enumerates the 'Plug-ins' folder next to the application for dynamic libraries (or bundles). Also, Vectorworks traverses shortcuts (aliases) placed in the folder hierarchy.

Each Plug-in module is a [VCOM (Vectorworks Component Object Model)](Info/VCOM%20(Vectorworks%20Component%20Object%20Model).md) provider library. This library provides one or many VCOM implementations of standard interfaces for the different extensions.

[More information on the topic see here](Info/Plug-in%20Module.md).

## VWFC

[VectorWorks Foundation Classes](Info/VectorWorks%20Foundation%20Classes.md)

## Version Information

- [Vectorworks 2025](Versions/Vectorworks%202025%20Development.md)
- [Vectorworks 2024](Versions/Vectorworks%202024%20Development.md)
- [Vectorworks 2023](Versions/Vectorworks%202023%20Development.md)
- [Vectorworks 2022](Versions/Vectorworks%202022%20Development.md)
- [Vectorworks 2021](Versions/Vectorworks%202021%20Development.md)
- [Vectorworks 2020](Versions/Vectorworks%202020%20Development.md)
- [Vectorworks 2019](Versions/Vectorworks%202019%20Development.md)
- [Vectorworks 2018](Versions/Vectorworks%202018%20Development.md)