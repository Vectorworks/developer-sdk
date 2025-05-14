# Create File Identifier

First, you should have a file identifier **IFileIdentifier** that will identify the file you want to work on.

Note that **IFileIdentifier** can identify non-existing files on the disk. See [Working with File Identifiers](VCOM:Working with File Identifiers).

# Raw File Operations

## Reading

```cpp
IRawOSFilePtr pFile( IID_RawOSFile );
if ( VCOM_SUCCEEDED( pFile->Open( pFileID, true, false, false, false ) ) ) {
    // read the entire file as 32-bit unsigned values
    Uint32   data;
    Uint64   readSize     = sizeof(data);

    Uint64   filePosition = 0;
    while( VCOM_SUCCEEDED( pFile->Read( filePosition, readSize, & data ) ) ) {
        if ( readSize != sizeof(data) ) {
           // the read size is not expected
           // so STOP!!!
           break;
        }

        // ...
        // work with 'data'

        // prepare reading next number
        filePosition   += readSize;
    }

    pFile->Close();
}
```

## Writing

```cpp
// Add your writing code here
```

# Standard File Operations

## Reading

```cpp
VCOMPtr<IStdFile>  pFile( IID_StdFile );
if ( VCOM_SUCCEEDED( pFile->OpenRead( pFileID, false ) ) ) {
  TXString   strLine;
  while( VCOM_SUCCEEDED( pFile->ReadStringLine( strLine ) ) ) {
     // ...
  }

  pFile->Close();
}
```

## Writing

```cpp
IStdFilePtr	pFile( IID_StdFile );
if ( VCOM_SUCCEEDED( pFile->OpenNewWrite( pFileID ) ) ) {
   // put two lines terminating by the default line term symbol
   pFile->WriteStringLine( "first line" );
   pFile->WriteStringLine( "second line" );

   pFile->Close();
}
```
