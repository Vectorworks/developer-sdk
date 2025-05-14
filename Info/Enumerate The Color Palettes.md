By [Vladislav Stanev](mailto:vstanev@nemetschek.net)

# What's that

Vectorworks ships with a set of default color palettes defined as XML files in the default content folders. Each color palette contains a set of named color definitions.

Each Vectorworks document has a color palette defined within the document. This palette is accessible through the [VCOM (Vectorworks Component Object Model)](https://developer.vectorworks.net/index.php/VCOM) `IDocumentColorTable` interface. The document color palette is dynamically modified by Vectorworks as new colors are being used by the user.

## Enumerate Default Color Palettes Available in Vectorworks

```cpp
using namespace VectorWorks;
using namespace VectorWorks::Filing;

class CColorPalettesCollection : public IFolderContentListener
{
public:
  virtual EFolderContentListenerResult  OnFolderContent(IFolderIdentifier* folderID)
  {
    return eFolderContentListenerResult_StopNoError;
  }

  virtual EFolderContentListenerResult  OnFileContent(IFileIdentifier* fileID)
  {
    IXMLFilePtr  xmlFile( IID_XMLFile );
    if ( VCOM_SUCCEEDED( xmlFile->ReadFile( fileID ) ) )
    {
      SColorPaletteDef  colorPaletteDef;
      xmlFile->GetSimpleValue( "ColorPalette/N", colorPaletteDef.fName );

      TXString      nodePath, nodeValue;
      TXStringSTLArray  arrTokens;
      for(size_t i=0; ; ++i)
      {
        SColorRecord  colorRecord;

        nodePath.Format( "ColorPalette/ColorList/C[%d]/N", i );
        colorRecord.fColorName  = xmlFile->GetSimpleValue( nodePath, "" );
        if ( colorRecord.fColorName.IsEmpty() )
          break;  // STOP!

        nodePath.Format( "ColorPalette/ColorList/C[%d]/D", i );
        nodeValue  = xmlFile->GetSimpleValue( nodePath, "" );
        if ( nodeValue.IsEmpty() )
          break;  // STOP!

        TXStringExt::Tokenize( nodeValue, arrTokens, ",", 1 );
        if ( arrTokens.size() != 4 )
          break;  // STOP!

        colorRecord.fCyan    = (float) arrTokens[ 0 ].atof(); 
        colorRecord.fMagenta = (float) arrTokens[ 1 ].atof();
        colorRecord.fYellow  = (float) arrTokens[ 2 ].atof();
        colorRecord.fKey     = (float) arrTokens[ 3 ].atof();

        colorPaletteDef.farrColors.push_back( colorRecord );
      }

      farrColorPaletteDefs.push_back( colorPaletteDef );
    }
  
    return eFolderContentListenerResult_Continue;
  }

private:
  typedef std::vector<SColorRecord>  TColorRecordsArray;

  struct SColorPaletteDef
  {
    TXString            fName;
    TColorRecordsArray  farrColors;
  };

  typedef std::vector<SColorPaletteDef>  TColorPaletteDefsArray;

  TColorPaletteDefsArray  farrColorPaletteDefs;
};
```

```cpp
void CMenu_EventSink::DoInterface()
{
  CColorPalettesCollection  theCollection;

  // collect all color palettes
  IApplicationFoldersPtr  appFolders( IID_ApplicationFolders );
  appFolders->ForEachFileInStandardFolder( &theCollection, kDefaultColorPalettesFolder );
}
```

## Enumerate The Color Palette of the Current Document

```cpp
void CMenu_EventSink::DoInterface()
{
  CRGBColor	myColor( 255, 0, 0 );

  VCOMPtr<IDocumentColorTable>	documentColorTable( IID_DocumentColorTable );

  SColorRecord	colorRecord;
  documentColorTable->GetColorRecord( myColor.GetColorIndex(), colorRecord );
}
```

## Working with the CMYK Color of SColorRecord

```cpp
SColorRecord	colorRecord;
TGSColorV2	rgbColor( colorRecord.fCyan, colorRecord.fMagenta, colorRecord.fYellow, colorRecord.fKey );
rgbColor.ConvertTo( TGSColorV2::eRGB );

Uint8 r, g, b;
rgbColor.GetRGB8( r, g, b );
