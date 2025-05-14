# Working with File Identifiers

## What is this?

File identifier interfaces are: `IFolderIdentifier` and `IFileIdentifier`.

They are used to identify a folder location (or folder path) or file location (or file path) on the OS disk.

The location stored in those identifier interfaces may not actually exist on the disk.

The interfaces are OS independent and should work the same on all platforms.

## Creating Interface Instance

The file identifier interfaces are part of `VCOM` (VectorWorks Component Object Model).

Good style is always to use `VCOMPtr` that will manage the interface reference correctly.

```cpp
// create new instance of the folder identifier interface
IFolderIdentifierPtr pFolderID( IID_FolderIdentifier );

// create new instance of the file identifier instance
IFileIdentifierPtr   pFileID( IID_FileIdentifier );
```

## Setting the Identifier

To set a new file or folder path (location), the functions `IFileIdentifier::Set` and `IFolderIdentifier::Set` are used.

These functions can set full paths (OS dependent) or work with relative paths according to VectorWorks installed locations.

For path delimiters, you should use `/` or `\` on Windows. `/` is recognized on Windows too, and it is preferable to use `/` for relative paths.

When building relative paths, you can use `..` to denote one folder up.

**Note:** `:` is not recognized as a path delimiter!

### Invalid Characters

On Windows, invalid characters are substituted as follows:

| Invalid Character | Substituted with |
|-------------------|------------------|
| `/`               | `%5C`           |
| `\`               | `%2F`           |
| `:`               | `%3A`           |
| `*`               | `%2A`           |
| `?`               | `%3F`           |
| `"`               | `%22`           |
| `<`               | `%3C`           |
| `>`               | `%3E`           |
| `|`               | `%7C`           |

On macOS, the only invalid folder name character is `:`. However, if you include that character in the folder path, it will result in the folder name having `/` at that place.

On macOS, if you have a **CFStringRef** string representation, you must convert it into a UTF-8 encoded `TXString` like this:

```cpp
TXString GetTXStringPath(const CFStringRef& strRef)
{
  TXString    result;
  const char* tempString = CFStringGetCStringPtr( strRef, kCFStringEncodingUTF8 );
  if (tempString) {
    result            = tempString;		
  }
  else {
    CFIndex  length    = CFStringGetLength( strRef );
    size_t   maxChars  = UnicodeToStringMaxChars( length );
    char*    buffer    = new char[ maxChars ];
    CFStringGetCString( strRef, buffer, maxChars, kCFStringEncodingUTF8 );
    result             = buffer;
    delete [] buffer;		
  }
  return result;
}
```

### Folder Specifiers

The enumeration `EFolderSpecifier` describes default locations for VectorWorks. Here is a list of constants defined in that enumeration:

| Constant Name                     | Value | Description                                                                 |
|-----------------------------------|-------|-----------------------------------------------------------------------------|
| `kRootFolderSpecifier`            | 0     |                                                                             |
| `kApplicationFolder`              | 1     | The folder where `VectorWorks.exe` is located.                             |
| `kExternalsFolder`                | 2     | The 'Plug-ins' folder located where `VectorWorks.exe` is located.          |
| `kExternalDataFolder`             | 3     | The 'Data' folder located inside the `kExternalsFolder` folder.            |
| `kWorkspacesFolder`               | 4     | The 'Workspace' folder located where `VectorWorks.exe` is located.         |
| `kOverlaysFolder`                 | -     | Same as `kWorkspacesFolder`.                                               |
| `kParametricsFolder`              | 5     | The 'Plug-ins' folder located where `VectorWorks.exe` is located.          |
| `kDebugtoolsFolder`               | 6     |                                                                             |
| `kTemplatesFolder`                | 7     |                                                                             |
| `kStandardsFolder`                | 8     |                                                                             |
| `kHelpFolder`                     | 9     | The 'Help' folder located where `VectorWorks.exe` is located.              |
| `kDictionariesFolder`             | 10    |                                                                             |
| `kObjectLibrariesFolder`          | 11    |                                                                             |
| `kAppDataFolder`                  | 12    | The "version/year number" folder inside the VectorWorks user folder.       |
| `kLibrariesFolder`                | 13    | The 'Libraries' folder located where `VectorWorks.exe` is located.         |
| `kDefaultsFolder`                 | 14    | The 'Defaults' folder located inside the `kLibrariesFolder` folder.        |
| `kSettingsFolder`                 | 15    | The 'Settings' folder located where `VectorWorks.exe` is located.          |
| `kDefaultFramingMemberProfileFolder` | 138 |                                                                             |
| `kDefaultEventLayoutSymbolsFolder` | 139 |                                                                             |
| `kDefaultSpacesOccupantOrgFolder` | 140   |                                                                             |
| `kDefaultSpacesNameListsFolder`   | 14    |                                                                             |

## Sample

```cpp
// create new instance of the folder identifier interface
IFolderIdentifierPtr pFolderID( IID_FolderIdentifier );
if ( VCOM_SUCCEEDED( pFolderID->Set( kExternalsFolder, false, "MyFolder" ) ) ) {
   // ...
   // if VectorWorks is installed at "C:\Program Files\VectorWorks" (Windows)
   // then the result folder will be "C:\Program Files\VectorWorks\Plug-ins\MyFolder"
}

// create new instance of the file identifier instance
IFileIdentifierPtr   pFileID( IID_FileIdentifier );
if ( VCOM_SUCCEEDED( pFolderID->Set( kExternalsFolder, false, "MyFolder/MyFile.txt" ) ) ) {
   // ...
   // if VectorWorks is installed at "C:\Program Files\VectorWorks" (Windows)
   // then the result file location will be "C:\Program Files\VectorWorks\Plug-ins\MyFolder\MyFile.txt"
}
```

### Setting Location Inside User Folder

```cpp
IFileIdentifierPtr   pFileID( IID_FileIdentifier );
if ( VCOM_SUCCEEDED( pFolderID->Set( kExternalsFolder, true, "MyFolder/MyFile.txt" ) ) ) {
   // ...
   // if the current user of Windows Vista is 'user'
   // then the result file location will be "C:\Users\user\AppData\Roaming\Nemetschek\VectorWorks\2009\Plug-ins\MyFolder\MyFile.txt"
}
```

### Using Relative Path

```cpp
IFileIdentifierPtr   pFileID( IID_FileIdentifier );
if ( VCOM_SUCCEEDED( pFolderID->Set( kExternalsFolder, false, "../../MyFolder/MyFile.txt" ) ) ) {
   // ...
   // if VectorWorks is installed at "C:\Program Files\VectorWorks" (Windows)
   // the result path is "C:\Program Files\VectorWorks\..\..\MyFolder\MyFile.txt"
   // then the result file location will be "C:\Program Files\MyFolder\MyFile.txt"
}
