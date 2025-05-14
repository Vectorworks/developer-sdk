# Adding Images to the List Browser Control

The images are to be added at initialization time to the control, and later they are used by IDs. Normally, the IDs are by the order the images are added:

There are two functions for adding images in the list browser control:

```cpp
// Adds image from ICN# resource.
// (You need to put icl8s image 
// along with a mask for each ICN# resource)
size_t AddImage(size_t resourceID,
                size_t resourceIDSelected = 0);
```

```cpp
// Adds standard image in the image list for this control
// These images are defined by VectorWorks
size_t AddImageStandard(EStandardImageID image,
                        EStandardImageID selImage
                                     = kStandardImageID_None);
```

Here is a list of available standard images:

```cpp
enum EStandardImageID {
	kStandardImageID_None,
	kStandardImageID_Blank,              // blank icon used for clearing
	kStandardImageID_DiamondBlack,
	kStandardImageID_DiamondHollow,
	kStandardImageID_DiamondGray,
	kStandardImageID_RedCrossedCircle,
	kStandardImageID_CheckMark,
	kStandardImageID_CheckMarkWhite,
	kStandardImageID_LeftBlackArrow,
	kStandardImageID_RightBlackArrow,
	kStandardImageID_LeftHollowArrow,
	kStandardImageID_RightHollowArraw,
	kStandardImageID_LeftThinArrow,
	kStandardImageID_RightThinkArrow,
	kStandardImageID_BallBlack,
	kStandardImageID_BallHollow,
	kStandardImageID_Slash,
	kStandardImageID_XMark,
};
```

Now you initialize your list browser control in the `Setup` event of the dialog:

```cpp
void CSampleDlg::OnSetUpEvent()
{
	VWDialog::OnSetUpEvent();

	VWListBrowserCtrl*	pListBrowser	= this->GetListBrowserCtrlByID( kMyListBrowsr );

	// filling up the image list
	pListBrowser->AddImageStandard( kStandardImageID_DiamondBlack );
	pListBrowser->AddImageStandard( kStandardImageID_DiamondHollow );
	pListBrowser->AddImageStandard( kStandardImageID_DiamondGray );

	// ...
```

**Note:** The order of filling in the images determines the index that this icon is represented with. In this example, the `DiamondBlack` icon has an index of 0; `DiamondHollow` has an index of 1; and `DiamondGray` has an index of 2.

After that, you need to fill up the columns in the list browser control:

## Adding Text Containing Column

```cpp
VWListBrowserColumn  col0  = pListBrowser->AddColumn( "None", 100 );
```

## Adding Image Containing Column

```cpp
VWListBrowserColumn col0  = m_pListBrowser->AddColumn( "Header" , 20);
col0.SetColumnType(kListBrowserControlNone);
col0.SetItemDisplayType(kListBrowserDisplayImageOnly);
```

## Adding Radio Changing Images Column

```cpp
VWListBrowserColumn  col1  = pListBrowser->AddColumn( "Radio", 100 );
col1.SetColumnType( kListBrowserControlRadio );

col1.InsertColumnChoice( "a", 0 );
col1.InsertColumnChoice( "b", 1 );
col1.InsertColumnChoice( "c", 2 );

col1.EnableRadioColumnLines( true );
```

- **InsertColumnChoice**: Puts a set of images that will alternate within this column. Note that the name of each choice (the text value) should be unique within the column.
- **EnableRadioColumnLines**: When calling this with `true`, you enable dividing lines between the choices to be drawn.

**Note:** When you fill the list with items, you should use the function:

```cpp
pListBrowser->GetItem( rowIndex, colIndex ).SetItemUsingColumnDataItem( imageIndex );
```

to set up the initial state of the radio column item image.

## Adding Multi-State Column

```cpp
VWListBrowserColumn  col2  = pListBrowser->AddColumn( "MultiState", 100 );
col2.SetColumnType( kListBrowserControlMultiState );
col2.SetItemDisplayType( kListBrowserDisplayImageAndText );

col2.InsertColumnChoice( "aa", 0 );
col2.InsertColumnChoice( "bb", 1 );
col2.InsertColumnChoice( "cc", 2 );
```

- **SetItemDisplayType**: You can mix up this column's values with text only, image only, or text and image (`kListBrowserDisplayImageOnly`, `kListBrowserDisplayTextOnly`, `kListBrowserDisplayImageAndText`).
- **InsertColumnChoice**: Adds the alternatives that this column may contain. The choices are switched by the user's click of the mouse on each row's column.

## Adding Single Instance Column

```cpp
VWListBrowserColumn  col3  = pListBrowser->AddColumn( "SingleInstance", 100 );
col3.SetColumnType( kListBrowserControlSingleInstance );
col3.SetItemDisplayType( kListBrowserDisplayImageAndText );
 
col3.InsertColumnChoice( "aaa", 0 );
col3.InsertColumnChoice( "bba", 1 );
col3.InsertColumnChoice( "cca", 2 );
```

## Adding 'NoClick' Column

```cpp
VWListBrowserColumn  col4  = pListBrowser->AddColumn( "NoClick", 100 );
col4.SetColumnType( kListBrowserControlNoClick );
```

## Adding 'Number' Column

```cpp
VWListBrowserColumn  col5  = pListBrowser->AddColumn( "Number", 100 );
col5.SetColumnType( kListBrowserControlNumber );
 
col5.SetAsDragAndDropColumn();
```

**Note:** This is the column that can be used to drag & drop. To do that, you have to mark the column with the `SetAsDragAndDropColumn` call. And you have to enable the drag & drop support for the control:

```cpp
pListBrowser->EnableDragAndDrop( true );
```

## Adding Multiple Images Column

```cpp
VWListBrowserColumn  col6  = pListBrowser->AddColumn( "MultImages", 100 );
col6.SetColumnType( kListBrowserControlMultipleImages );
col6.SetItemDisplayType( kListBrowserDisplayImageOnly );

col6.InsertColumnChoice( "aaaaaa", 0 );
col6.InsertColumnChoice( "bbabba", 1 );
col6.InsertColumnChoice( "ccacca", 2 );
```

## Adding Color Column

```cpp
VWListBrowserColumn clrCol = pListBrowser->AddColumn( "Color", 100 );
clrCol.SetColumnType( kListBrowserControlNoClick );
clrCol.SetItemDisplayType( kListBrowserDisplayImageOnly );

...

VWListBrowserItem	colorItem	= pListBrowser->GetItem( index, 2 );
colorItem.SetOwnerDrawnType( kListBrowserSolidRect );
colorItem.SetItemFillBackColor( fColorBack );
colorItem.SetItemFillForeColor( fColorFore );
colorItem.SetItemPenBackColor( fColorBack );
colorItem.SetItemPenForeColor( fColorFore );
```

## Adding Line Style Column

```cpp
VWListBrowserColumn	lineCol	= pListBrowser->AddColumn( "Line Style", 70 );
lineCol.SetColumnType( kListBrowserControlNoClick );
lineCol.SetItemDisplayType( kListBrowserDisplayImageOnly );

...

CRGBColor dashForeColor;
dashForeColor.SetFrontColor();

VWListBrowserItem itemLineStyle = pListBrowser->GetItem( itemIndex, 3 );
itemLineStyle.SetOwnerDrawnType( kListBrowserDashedLine );
itemLineStyle.SetItemDashStyle( fLineStyle, fLineWeight );
itemLineStyle.SetItemPenForeColor( dashForeColor );
```

**Update:** After the line styles became resources, the `SetItemDashStyle` doesn't work anymore. You should use `gSDK->SetListBrowserItemLineTypeRef` until the SDK is updated to accommodate for this.

```cpp
InternalIndex fLineStyle = ...;

VWListBrowserItem itemLineStyle = pListBrowser->GetItem( itemIndex, 3 );
itemLineStyle.SetOwnerDrawnType( kListBrowserDashedLine );
gSDK->SetListBrowserItemLineTypeRef( this->GetDialogID(), pListBrowser->GetControlID(), Sint32(itemIndex), Sint32(3), fLineStyle < 0 ? (-1 * fLineStyle) : 0, fLineWeight );
itemLineStyle.SetItemPenForeColor( dashForeColor );
```

# Working with Items

```cpp
pListBrowser->GetItem( 0, 0 ).SetItemText( "text 0" );
pListBrowser->GetItem( 0, 5 ).SetItemText( "0" );
pListBrowser->GetItem( 0, 6 ).SetMultImageIndexes( 1534, 1535, 1536 );

pListBrowser->GetItem( 1, 0 ).SetItemText( "text 1" );
pListBrowser->GetItem( 1, 5 ).SetItemText( "1" );
pListBrowser->GetItem( 1, 6 ).SetMultImageIndexes( 2, 1 );

pListBrowser->GetItem( 2, 0 ).SetItemText( "text 2" );
pListBrowser->GetItem( 2, 5 ).SetItemText( "2" );
```

An example of single image columns:
```cpp
size_t check = pListBrowser->AddImageStandard(kStandardImageID_CheckMark);
size_t uncheck = pListBrowser->AddImageStandard(kStandardImageID_Blank);

...

pListBrowser->GetItem( 0, 0 ).SetItemImage(check);
pListBrowser->GetItem( 1, 0 ).SetItemImage(uncheck);
```

# Creating a Check Mark Column

```cpp
// list browser constants
#define		kChecked	"Yes"
#define		kUncheced	"No"

size_t	fImageIndexBlank;
size_t	fImageIndexCheckmark;


	fImageIndexBlank	= fListCtrl->AddImageStandard( kStandardImageID_Blank );
#if GS_WIN
	fImageIndexCheckmark	= fListCtrl->AddImageStandard( kStandardImageID_CheckMark, kStandardImageID_CheckMarkWhite );
#else
	fImageIndexCheckmark	= fListCtrl->AddImageStandard( kStandardImageID_CheckMark );
#endif

	VWListBrowserColumn  columnCheck  = fListCtrl->AddColumn( resLoader.Load(kStr_Visible), 120 );
	columnCheck.SetColumnType( kListBrowserControlMultiState );
	columnCheck.SetItemDisplayType( kListBrowserDisplayImageOnly );
	columnCheck.InsertColumnChoice( kUncheced, fImageIndexBlank );
	columnCheck.InsertColumnChoice( kChecked, fImageIndexCheckmark );
```

If you are after implemented radio control behavior with image column, set the following to the column:

```cpp
	columnCheck.SetColumnType( kListBrowserControlSingleInstance );
```

# Drag & Drop Column

Drag and Drop is a property of the column but you also need to enable it in the control:

```cpp
VWListBrowserCtrl*	settingsList	= this->GetListBrowserCtrlByID( kSettingsList );
settingsList->EnableDragAndDrop( true );

VWListBrowserColumn  columnNum  = settingsList->AddColumn( resLoader.Load(kStr_ColNumber), 30 );
columnNum.SetColumnType( kListBrowserControlNumber );
columnNum.SetAsDragAndDropColumn();
```

Then you need to handle the event coming from the list browser control. Note, we are relying on the first column to be the numeric column for the drag&drop, plus to contain indices that refer to our local data for the list (farrParamsLocalData)

```cpp
void CMyDialog::OnParametersList(long controlID, VWDialogEventArgs& eventArgs)
{
	VWListBrowserCtrl*	settingsList	= this->GetListBrowserCtrlByID( kSettingsList );
	
	if ( eventArgs.IsListBrowserDoubleClickEvent() )
	{
		this->OnEditBtn( controlID, eventArgs );
	}
	else if ( eventArgs.IsListBrowserDrawNDropEnd() )
	{
		TEditFiledLocalDataArray	parametersOriginal = farrParamsLocalData;

		farrParamsLocalData.clear();
		size_t	rowsCnt = settingsList->GetRowsCount();
		for(size_t i=0; i<rowsCnt; i++)
		{
			long	originalIndex	= settingsList->GetItem( i, 0 ).GetItemText().atoi() - 1;

			VERIFYN( kVStanev, originalIndex >= 0 && originalIndex < parametersOriginal.size() );
			const SEditFieldLocalData&	itemData = parametersOriginal[ originalIndex ];
			farrParamsLocalData.push_back( itemData );
		}

		// rebuild the list and select the necessary rows
		this->UpdateParametersList( );
	}
}
```

Also, there is a function to refill the list updating the numbers on the drag&drop column. This function also preserves the original selection:

```cpp
void CMyDialog::UpdateParametersList()
{
	VWListBrowserCtrl*	settingsList	= this->GetListBrowserCtrlByID( kSettingsList );

	TSizeTArray		arrSelIndices;
	TXStringSTLArray	arrSelNames;

	settingsList->GetSelectedItemIndices( arrSelIndices );
	for(size_t i=0, cnt=arrSelIndices.size(); i<cnt; i++)
	{
		TXString	selName = settingsList->GetItem( arrSelIndices[i], 2 ).GetItemText();
		arrSelNames.push_back( selName );
	}

	arrSelIndices.clear();
	settingsList->DeleteAllRows();

	for(size_t i=0, cnt=farrParamsLocalData.size(); i<cnt; i++)
	{
		const SEditFieldLocalData&	paramData = farrParamsLocalData[ i ];

		size_t rowIndex = settingsList->AddRow( TXStringExt::Format( "%d", i + 1 ) );
		settingsList->GetItem( rowIndex, 1 ).SetItemImage( paramData.fUse? fImageIndexCheckmark : fImageIndexBlank );
		settingsList->GetItem( rowIndex, 2 ).SetItemText( paramData.fName );
		settingsList->GetItem( rowIndex, 3 ).SetItemText( paramData.fPrettyName );
		settingsList->GetItem( rowIndex, 3 ).SetItemText( paramData.fType);
		settingsList->GetItem( rowIndex, 5 ).SetItemText( paramData.fDefaultValue );

		if ( std::find( arrSelNames.begin(), arrSelNames.end(), paramData.fName ) != arrSelNames.end() )
		{
			arrSelIndices.push_back( i );
		}
	}

	settingsList->SetSelectedItemIndices( arrSelIndices, true );
}
```

Alternatively, you can use provided data for the drag&drop indices:

```cpp
void  CMyDialog::OnParamsList(long controlID, VWFC::VWUI::VWDialogEventArgs& eventArgs)
{
	static bool sWaitDropInfoMessage = false;
	static VWDialogEventArgs::SListBrowserDragDropInfo sDropInfo;
	if ( sWaitDropInfoMessage )
	{
		VWDialogEventArgs::SListBrowserDragDropInfo* dropInfo = eventArgs.GetListBrowserDropInfo();
		if ( dropInfo )
			sDropInfo = *dropInfo;

		sWaitDropInfoMessage = false;
	}
	else if ( eventArgs.IsListBrowserNextMessageIsDropInfo() )
	{
		sWaitDropInfoMessage = true;
	}
	else if ( eventArgs.IsListBrowserDrawNDropEnd() )
	{
		VWListBrowserCtrl*	paramsList		= this->GetListBrowserCtrlByID( kParametersList );

		size_t	paramsCnt		= farrParameters.size();
		size_t	dragParamIndex	= sDropInfo.iDragIndex >= 0 ? paramsList->GetRowMarker( sDropInfo.iDragIndex ) : size_t(-1);
		size_t	dropParamIndex	= sDropInfo.iDropIndex >= 0 ? paramsList->GetRowMarker( sDropInfo.iDropIndex ) : size_t(-1);
		if ( VERIFYN( kVStanev, dragParamIndex < paramsCnt && dropParamIndex < paramsCnt ) )
		{
			const FieldSpec& dragParamDef = farrParameters[ dragParamIndex ];

			if ( dropParamIndex + 1 <= dragParamIndex )
			{
				farrParameters.insert( farrParameters.begin() + dropParamIndex, dragParamDef );
				farrParameters.erase( farrParameters.begin() + dragParamIndex + 1 );

				this->FillParameters( true, dropParamIndex );
			}
			else 
			{
				farrParameters.insert( farrParameters.begin() + dropParamIndex + 1, dragParamDef );
				farrParameters.erase( farrParameters.begin() + dragParamIndex );

				this->FillParameters( true, dropParamIndex );
			}
		}
	}
	else if ( eventArgs.IsListBrowserDoubleClickEvent() )
	{
		this->OnParamEditBtn( controlID, eventArgs );
	}
	else
	{
		// this is just a protection!
		sWaitDropInfoMessage = false;
	}
}
```

This uses calls that might be missing in older VWFC versions. However, they can still be used:

```cpp
struct SListBrowserDragDropInfo
{
	std::vector<int>		vIndicesDragged;
	int						iDragIndex;
	int						iDropIndex;
	int						iSortState;

	SListBrowserDragDropInfo() { iDragIndex = iDropIndex = iSortState = -1; }
};

bool VWDialogEventArgs::IsListBrowserNextMessageIsDropInfo() const
{
	return this->GetEventData() == -50;
}

VWDialogEventArgs::SListBrowserDragDropInfo* VWDialogEventArgs::GetListBrowserDropInfo()
{
	return (SListBrowserDragDropInfo*) this->GetEventData();
}
```

# See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
