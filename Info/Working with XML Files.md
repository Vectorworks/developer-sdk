# XML Files

[XML on Wikipedia](http://en.wikipedia.org/wiki/XML)

VectorWorks provides the following VCOM interfaces for working with XML files:

- **IXMLFile** -- Represents an XML file.
- **IXMLFileNode** -- Represents a node in an XML file.
- **IXMLFileValidator** -- Validating XML files.

## Using XML File

When you have **[IFileIdentifier** to an XML file, you can use **IXMLFile::ReadFile** to read or write the XML contents.

### Reading

#### Reading XML file sample:
```cpp
IXMLFilePtr        pXMLFile( IID_XMLFile );
if ( pXMLFile && VCOM_SUCCEEDED( pFile->ReadFile( fpFileID ) ) ) {
    IXMLFileNodePtr        pDatabaseRoot;
    if ( VCOM_SUCCEEDED( pFile->GetRootNode( & pDatabaseRoot ) ) ) {
       TXString    rootName;
       if ( VCOM_SUCCEEDED( pDatabaseRoot->GetNodeName( rootName ) ) ) {
   
           // get UUID and Name attributes
           TXString    dbUUID, dbName;
           pDatabaseRoot->GetNodeAttributeValue( kXmlName_UUID, dbUUID );
           pDatabaseRoot->GetNodeAttributeValue( kXmlName_Name, dbName );

           IXMLFileNodePtr        pNote;
           if ( VCOM_SUCCEEDED( pDatabaseRoot->GetFirstChildNode( & pNote ) ) ) {
               while ( pNote ) {
                   TXString            noteSectionName;
                   TXString            noteUUID;
                   TXString            noteDescription;
                   TXString            noteText;
                   pNote->GetNodeAttributeValue( kXmlName_SectionName, noteSectionName );
                   pNote->GetNodeAttributeValue( kXmlName_UUID, noteUUID );
                   pNote->GetNodeAttributeValue( kXmlName_Desc, noteDescription );
                   pNote->GetNodeValue( noteText );

                   // get the next xml node
                   IXMLFileNodePtr        pNextNote;
                   if ( VCOM_FAILED( pNote->GetNextSiblingNode( & pNextNote ) ) )
                       break;

                   // move to next note
                   pNote        = pNextNote;
               }
           }
       }
   }
}
```

#### Reading arrays for nodes:
```cpp
IXMLFileNodePtr		pXMLTablesNode;
if ( VCOM_SUCCEEDED( pLayoutData->GetChildNode( kDBXML_LayoutDataNode_Tables, & pXMLTablesNode ) ) ) {
	IXMLFileNodePtr	pXMLTableNode;
	if ( VCOM_SUCCEEDED( pXMLTablesNode->GetChildNode( kDBXML_LayoutDataNode_Table, & pXMLTableNode ) ) ) {
		do {
			// ...
			// move to the next param
			IXMLFileNodePtr	  pXMLTableNextNode;
			pXMLTableNode->GetNextSiblingNode( & pXMLTableNextNode );
			pXMLTableNode		= pXMLTableNextNode;
		} while ( pXMLTableNode );
	}
}
```

### Writing

#### Writing XML file sample:
```cpp
bool CXmlSerializer::WriteToXML(IXMLFile* pFile, const CDatabase& database)
{
   bool       ok         = false;
   if ( VCOM_SUCCEEDED( pFile->CreateNew( kXmlName_DBName ) ) ) {
       
       IXMLFileNodePtr        pDatabaseRoot;
       
       if ( VCOM_SUCCEEDED( pFile->GetRootNode( & pDatabaseRoot ) ) ) {

           pDatabaseRoot->SetNodeAttributeValue( kXmlName_UUID, database.GetUUID() );
           pDatabaseRoot->SetNodeAttributeValue( kXmlName_Name, database.GetName() );

           size_t        secCnt        = database.GetSectionsCount();
           for(size_t s=0; s<secCnt; s++) {
               TXString    sectionName    = database.GetSectionNameAt( s );
               size_t        notesCnt    = database.GetNotesCount( s );
               for(size_t j=0; j<notesCnt; j++) {
                   const CDatabaseNote&    note        = database.GetNoteAt( s, j );

                   IXMLFileNodePtr        pNoteNode;
                   if ( VCOM_SUCCEEDED( pDatabaseRoot->CreateChildNode( kXmlName_Note, & pNoteNode ) ) ) {
                       pNoteNode->SetNodeAttributeValue( kXmlName_SectionName, sectionName );
                       pNoteNode->SetNodeAttributeValue( kXmlName_UUID, note.GetUUID() );
                       pNoteNode->SetNodeAttributeValue( kXmlName_Desc, note.GetDescription() );

                       pNoteNode->SetNodeValue( note.GetNoteText() );
                   }
               }
           }
            
           if ( VCOM_SUCCEEDED( pXMLFile->WriteFile( pFileID, kXMLEncoding_UTF8 ) ) ) {
               ok     = true;
           }
       }
   }

   return ok;
}
```

#### Writing arrays for nodes:
```cpp
size_t			tablesCnt	= arrTables.Size();
if ( tablesCnt > 0 ) {
	IXMLFileNodePtr  	pXMLTablesNode;
	if ( VCOM_SUCCEEDED( pLayoutData->CreateChildNode( kDBXML_LayoutDataNode_Tables, & pXMLTablesNode ) ) ) {
		for(size_t i=0; i<tablesCnt; i++) {
			const SResourceTable&	table	= arrTables[ i ];

			IXMLFileNodePtr  	pXMLTableNode;
			if ( VCOM_SUCCEEDED( pXMLTablesNode->CreateChildNode( kDBXML_LayoutDataNode_Table, & pXMLTableNode ) ) ) {
				// ...
			}
		}
	}
}
```

## Using XML Memory Buffer

The [IXMLFile](VCOM:VectorWorks:Filing:IXMLFile) interface allows you to work with XML files or XML represented in memory buffers.

The memory buffer is represented by [IXMLFileIOBuffer](VCOM:VectorWorks:Filing:IXMLFileIOBuffer). This means that the user has to implement that interface supplying the memory and pass a pointer to the [IXMLFile::ReadBuffer](VCOM:VectorWorks:Filing:IXMLFile::ReadBuffer) or [IXMLFile::WriteBuffer](VCOM:VectorWorks:Filing:IXMLFile::WriteBuffer) calls.

### Example of memory buffer implementation:
```cpp
class CStringXMLData : public IXMLFileIOBuffer
{
public:
	CStringXMLData()
	{
	}

	CStringXMLData(const TXString& content)
	{
		fString = content;
	}

	virtual ~CStringXMLData()
	{
	}

	TXString GetString() const
	{
		return fString;
	}

	virtual	VCOMError VCOM_CALLTYPE	GetDataSize(size_t& outSize)
	{
		outSize		= fString.GetLength();
		return kVCOMError_NoError;
	}

	virtual VCOMError VCOM_CALLTYPE	CopyDataInto(void* pBuffer, size_t bufferSize)
	{
		char*		pOutBuffer	= (char*) pBuffer;

		const char*	szBuffer	= fString;
		size_t		copyLen		= fString.GetLength();
		if ( bufferSize < copyLen ) {
			copyLen	= bufferSize;
		}

		for(size_t i=0; i<copyLen; i++) {
			pOutBuffer[ i ] = szBuffer[ i ];
		}

		return kVCOMError_NoError;
	}

	virtual VCOMError VCOM_CALLTYPE	SetData(void* pBuffer, size_t bufferSize)
	{
		TXString	str( (const char*) pBuffer, bufferSize );
		fString		+= str;
		return kVCOMError_NoError;
	}

private:
	TXString	fString;
};
```

#### Reading:
```cpp
IXMLFilePtr	pXMLFile( IID_XMLFile );

CStringXMLData		data( strXMLText );
if ( VCOM_SUCCEEDED( pXMLFile->ReadBuffer( & data, eXMLEncoding_UTF8 ) ) ) {
	importOk  = this->ProcessFile( pXMLFile );
}
```

#### Writing:
```cpp
IXMLFilePtr	pXMLFile( IID_XMLFile );
if ( this->Export( pXMLFile ) ) {
	CStringXMLData	xmlOutput;
	exportOk	= VCOM_SUCCEEDED( pXMLFile->WriteBuffer( & xmlOutput, eXMLEncoding_UTF8 ) );

	outXMLContent	= xmlOutput.GetString();
}
```

## Validating

### Sample code for validating:
```cpp
IXMLFileValidatorPtr pXMLFileValidator( IID_XMLFileValidator );
IXMLFilePtr          pXMLFile( IID_XMLFile );
IFileIdentifierPtr   pXMLFileID( IID_FileIdentifier );
IFileIdentifierPtr   pXSDFileID( IID_FileIdentifier );
if (    pXMLFileValidator
     && pXMLFile
     && pXMLFileID && VCOM_SUCCEEDED( pXMLFileID->Set( "Test.xml" ) )
     && pXSDFileID && VCOM_SUCCEEDED( pXSDFileID->Set( "Test.xsd" ) )
   )
{
   VERIFYN( kVStanev, VCOM_SUCCEEDED( pXMLFileValidator->SetXML( pXMLFileID ) ) );
   VERIFYN( kVStanev, VCOM_SUCCEEDED( pXMLFileValidator->SetSchema( pXSDFileID ) ) );

   CMyXMLErrListener	errListener;
   VERIFYN( kVStanev, VCOM_SUCCEEDED( pXMLFileValidator->SetErrorListener( & errListener ) ) );

   VERIFYN( kVStanev, VCOM_SUCCEEDED( pXMLFileValidator->Validate() ) );
}
```

#### Test.xsd:
```xml
<?xml version="1.0" encoding="Windows-1252"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="Root">
    <xs:complexType mixed="true">
      <xs:sequence>
        <xs:element name="inner" type="xs:string" />
        <xs:element name="sub1" type="xs:string" />
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

#### Test.xml (will produce syntax error):
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no" ?>
<Root>
  <!-- the next line will produce "Unterminated end tag" error -->
  <inner>inner value</inner1>
  <sub1>sub value</sub1>
</Root>
```

## XML SAX Support

In Vectorworks XXXX, XML SAX Support is provided by the [IXMLSAXFile](VCOM:VectorWorks::Filing:IXMLSAXFile) interface. The XML SAX interface allows traversing of XML files without building a DOM model, which enables processing of enormous files.

### Reading through the XML SAX interface:
```cpp
// this listener will be notified for the contents of the XML
// as the parser goes through it.
// The XML parser will not store any nodes for future use
// so the user must store the information for each node as it comes
// having in mind memory consumption (this linear XML process can be used for huge XML files)
class CMyXMLContentsListener : public IXMLSAXListener
{
public:
    CMyXMLContentsListener()
    {
    }

    virtual ~CMyXMLContentsListener()
    {
    }

public:
    virtual void VCOM_CALLTYPE 	OnDocument(const bool isStart)
    {
        // isStart == true: the SAX parser starts processing an XML file 
        // isStart == false: the SAX parser ends processing an XML file 
    }

    virtual void VCOM_CALLTYPE 	OnNode(const bool isStart, const SXMLURI& uri, const TXString& name, IXMLSAXAttributes* attributes)
    {
        // isStart == true: the SAX parser starts processing an XML file node
        // isStart == false: the SAX parser ends processing an XML file node

        // the node information is located in 'name' and 'uri' parameters
        // also 'attributes' can be used to obtain node's attributes information
    }

    virtual void VCOM_CALLTYPE 	OnNodeValue(const TXString&  value)
    {
        // the SAX parser has found a value inside the last open node
    }
};

void myReadFunction()
{
    CMyXMLContentsListener  myXMLContentsListener ;

    IXMLSAXFilePtr    xmlSAXFile( IID_XMLSAXFile );
    xmlSAXFile->ParseFile( fileID, & myXMLContentsListener );
}
```

### Writing through the XML SAX interface:
```cpp
{ // SCOPE
  IXMLSAXFilePtr    xmlSAXFile( IID_XMLSAXFile );
  xmlSAXFile->BeginDocument( fileID, eXMLEncoding_UTF8 );
  xmlSAXFile->BeginNode( "rootNode" );
    xmlSAXFile->BeginNode( "info" );
      xmlSAXFile->AddAttribute( "id", "1" );
      xmlSAXFile->AddValue( "value1" );
    xmlSAXFile->EndNode();
  
    xmlSAXFile->BeginNode( "info" );
      xmlSAXFile->AddValue( "value2" );
    xmlSAXFile->EndNode();
  xmlSAXFile->EndNode();
  xmlSAXFile->EndDocument();

} // going out of scope will close the file
