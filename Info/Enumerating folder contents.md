# Enumerating Folder Contents

Having a valid **IFolderIdentifier** (see [Working with File Identifiers](Working%20with%20File%20Identifiers.md)) will allow you to use the **IFolderIdentifier::EnumerateContents** function to enumerate the folder contents.

## Create Folder Listener Class

Before enumerating, you have to create a class that will receive the folder contents. This is done by creating a class implementing the `IFolderContentListener` interface (see remarks of **IFolderIdentifier::EnumerateContents**).

```cpp
class CMyFolderListener : public IFolderContentListener
{
public:
    CMyFolderListener(int someData)
    {
        fSomeData = someData;
    }

    void GetFiles(TXStringArray& outArrFiles)
    {
        outArrFiles = farrFiles;
    }

    // IFolderContentListener
public:
    virtual EFolderContentListenerResult VCOM_CALLTYPE OnFolderContent(IFolderIdentifier* pFolderID)
    {
        TXString folderName;
        pFolderID->GetName(folderName);

        return eFolderContentListenerResult_Continue;
    }

    virtual EFolderContentListenerResult VCOM_CALLTYPE OnFileContent(IFileIdentifier* pFileID)
    {
        TXString fileName;
        pFileID->GetFileName(fileName);

        farrFiles.Append(fileName);

        return eFolderContentListenerResult_Continue;
    }

protected:
    int fSomeData;
    TXStringArray farrFiles;
};
```

This class extends `IFolderContentListener`, making it a folder listener. It is responsible for handling folder contents through the virtual functions `OnFolderContent` and `OnFileContent`.

Additionally, it can store local data that is received by the constructor and retrieved by a public member function.

## Enumerate

When you want to enumerate, create an instance of your listener class and call the **IFolderIdentifier::EnumerateContents** function of the `IFolderIdentifier` instance representing the folder you want to enumerate:

```cpp
IFolderIdentifierPtr pFolderID(IID_FolderIdentifier);
if (VCOM_SUCCEEDED(pFolderID->Set(kExternalsFolder, false, "MyFolder"))) {
    CMyFolderListener myContentsListener(32);
    pFolderID->EnumerateContents(&myContentsListener, false);

    TXStringArray arrFiles;
    myContentsListener.GetFiles(arrFiles);

    // ...
}
