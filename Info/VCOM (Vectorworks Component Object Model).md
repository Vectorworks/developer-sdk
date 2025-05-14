# What is this?

VCOM (Vectorworks Component Object Model) is an API to deliver component services in the Vectorworks runtime.

Component services are provided by Vectorworks or any installed module. Providers register services with the VCOM manager using universal unique identifiers. Clients then obtain access to the registered services in the form of a pointer to a class of virtual functions.

One that uses VCOM is called 'Client' and the one that provides interfaces to VCOM is called 'Provider'.

## How do I use it?

VCOM is part of the SDK. This means that each plug-in can use VCOM. Anyone who uses VCOM is called 'Client of VCOM'.

When a client needs to use a specific interface, first, it has to know its **VCOM:VWIID, to recognize the interface, and then call **GS_VWQueryInterface** to obtain a pointer to that interface.

`VWIID` is a UUID (Universal Unique Identifier) structure that uniquely identifies one interface and no other. All `VWIID` constant variables for interfaces always start with `IID_` and then the name of the interface.

Sample of a client querying a VCOM interface instance. Then this instance is used to make a call to a function, and the instance is released. Releasing the instance denotes that this interface instance is no longer in use by this code.

```cpp
IVCOMSample* pSampleInst = NULL;
if (VCOM_SUCCEEDED(::GS_VWQueryInterface(IID_VCOMSample, (IVWUnknown**) &pSampleInst))) {
    VCOMError vcomErr = pSampleInst->Function();

    pSampleInst->Release();
}
```

In this example, `IID_VCOMSample` is the UUID constant identifying the `IVCOMSample` interface.

**Note! You must always release queried interfaces. Failing to do so will cause memory leaks in the Vectorworks process.**

For easier use, there is a [VCOM:VCOMPtr](VCOM:VCOMPtr) template class that allows you to query and automatically release interfaces:

Here is an example of using it:

```cpp
VCOMPtr<IVCOMSample> pSampleInit(IID_VCOMSample); // This queries the interface,
                                                  // The interface instance
                                                  // will be automatically released
                                                  // by the destructor

if (pSampleInit) {                                // Determine if it contains
                                                  // valid interface pointer
    VCOMError vcomErr = pSampleInit->Function();
}
```

By definition (not always), each function for an interface returns a [VCOM:VCOMError](VCOM:VCOMError) error result. This result denotes if the function succeeded. To determine that, there are macros [VCOM_SUCCEEDED](VCOM:VCOMError#VCOM_SUCCEEDED) to determine success and [VCOM_FAILED](VCOM:VCOMError#VCOM_FAILED) to determine failure.

The common predefined VCOM errors are:

- `kVCOMError_NoError` - There was no error.
- `kVCOMError_Failed` - The operation failed due to a problem within the called function.
- `kVCOMError_OutOfMemory` - Out of memory.
- `kVCOMError_NotInitialized` - Either VCOM or the object is not initialized properly.
- `kVCOMError_NoInterface` - There is no interface with the desired `VWIID`.
- `kVCOMError_NotImplemented` - The function called is not implemented.
- `kVCOMError_DuplicateIID` - Duplicate `VWIID` when registering the interface.
- `kVCOMError_NoModule` - No module containing the interface.
- `kVCOMError_NoInstance` - No instance for the `VWIID`.
- `kVCOMError_Unexpected` - Unexpected error.
- `kVCOMError_InvalidArg` - Some of the parameters for the call are not valid (NULLs or bad values).

## How do I make an interface provider?

Providers of interfaces can be **only** Vectorworks module plug-ins. A module plug-in defines interface implementation using the template function **REGISTER_Interface**:

```cpp
extern "C" long GS_EXTERNAL_ENTRY plugin_main(long action, void* pInfo, long& ioData, CallBackPtr cbp)
{
    // initialize VCOM mechanism
    ::GS_InitializeVCOM(cbp);

    long reply = 0L;

    // Register interface implementations provided by this module
    REGISTER_Interface<CVCOMSample>(IID_VCOMSample, action, pInfo, ioData, cbp, reply);

    return reply;
}
```

- `CVCOMSample` -- this is the class that implements the interface.
- `IID_VCOMSample` -- this is the `VWIID` identifier that uniquely identifies the interface.

And the interface that this plug-in defines is defined like this:

```cpp
// --------------------------------------------------------------------------------------------------
// {B5706042-BF87-4cff-9BD9-3A08B01C183C}
static const VWIID IID_VCOMSample = { 0xb5706042, 0xbf87, 0x4cff, { 0x9b, 0xd9, 0x3a, 0x8, 0xb0, 0x1c, 0x18, 0x3c } };

class IVCOMSample : public IVWUnknown
{
public:
    virtual VCOMError VCOM_CALLTYPE Function() = 0;
};
```

The implementation should provide implementation for the two functions from [VCOM:IVWUnknown](VCOM:IVWUnknown).

**Note** the use of `CallbackPtr`. The interface implementation has an internal reference to that callback, which is guaranteed to be valid when the implementation is created:

```cpp
class CVCOMSample : public IVCOMSample
{
public:
    CVCOMSample(CallBackPtr pCBP)
    {
        fRefCnt = 0;
        fCBP = pCBP;
    }

    virtual ~CVCOMSample()
    {
    }

    virtual long VCOM_CALLTYPE AddRef()
    {
        fRefCnt++;
        return fRefCnt;
    }

    virtual long VCOM_CALLTYPE Release()
    {
        if (fRefCnt > 0) {
            fRefCnt--;
        }

        return fRefCnt;
    }

public:
    virtual VCOMError VCOM_CALLTYPE Function()
    {
        // use 'fCBP' for 'GS_' calls
        return kVCOMError_NoError;
    }

private:
    long fRefCnt;
    CallBackPtr fCBP;
};
```

## Where do I get the interface definitions?

The idea is for Vectorworks to provide a certain set of standard interfaces, allowing the plug-ins to be more flexible. Also, each plug-in can provide a separate set of interfaces that other plug-ins may use. This means, of course, that the client plug-in is dependent on the presence of the provider plug-in.

The standard interfaces that Vectorworks provides can be found inside *SDKLib\Include\Interfaces*.

Including *SDKLib\Include\Interfaces\VWInterfaces.h* will provide you with the VCOM interface definitions that Vectorworks provides.

The interfaces are put in a strict hierarchy of namespaces, dividing the interfaces according to their purpose.

See [VCOM:Class Reference](VCOM:Class Reference) for more information.

## Things that I should know

- **What is [VCOM:IVWUnknown](VCOM:IVWUnknown)?** - This is the base interface of all VCOM interfaces. One set of virtual functions is called a VCOM interface when it inherits `IVWUnknown`.

```cpp
class IVWUnknown
{
public:
    virtual long VCOM_CALLTYPE AddRef() = 0;
    virtual long VCOM_CALLTYPE Release() = 0;
};
```

- **Never forget to 'Release' queried interfaces.** When an interface is queried, the plug-in library provider is loaded into memory and doesn't get unloaded until all interface implementations that it provided have been released.

- **Use reference counting when passing interface pointers through classes and functions.**

```cpp
void MyClass::MyFunction(IMyInterface* pInterface)
{
    pInterface->AddRef();         // Increases the reference count of this interface
                                  // so it will not be deleted before this function releases it
                                  // This is very useful for thread safety

    // ... now work with pInterface

    pInterface->Release();       // Releases the use of this interface for this function
}
```

## Standard Vectorworks interfaces

Vectorworks provides a set of interfaces allowing plug-ins to communicate with Vectorworks.

You can see the interfaces that are currently supported in *SDKLib\Include\Interfaces*.

The standard interfaces are grouped within the `VectorWorks` namespace. Including *SDKLib\Include\Interfaces\VWInterfaces.h* will include all the interfaces supported.

Here is an example of using the interfaces for choosing a dialog and working with a file:

```cpp
IFileChooserDialogPtr pFileChoose(IID_FileChooserDialog);

pFileChoose->SetTitle(TXString(kStr_ExportVectorScript));
TXStringArray arrExts;
arrExts.Append("vs");
arrExts.Append("px");
pFileChoose->AddFilter(arrExts, "VectorScript Files");
pFileChoose->SetDefaultFileName(fOptions.fLayoutData.fName);
pFileChoose->SetDefaultExtension("vs");

if (VCOM_SUCCEEDED(pFileChoose->RunSaveDialog())) {
    IFileIdentifierPtr pVSFileID;

    if (VCOM_SUCCEEDED(pFileChoose->GetSelectedFileName(0, &pVSFileID))) {    
        IStdFilePtr pFile(IID_StdFile);

        pFile->SetDefaultNewLine(kLineEndType_Win);
        
        if (pFile->OpenNewWrite(pVSFileID)) {
            pFile->WriteStringLine("HELLO");

            pFile->Close();
        }
    }
}
