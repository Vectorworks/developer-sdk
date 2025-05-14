# Overview

This control is used to represent a list of hatch, gradient, or image resources.

A full example of it being used for all those types can be found in the SDK samples at:

```
\AppSource\Source\Plug-Ins\Sample\VWUI Dialogs Sample
```

## Adding Resource and Default Content

Filling the control is similar for all types. Here the examples are only for hatches.

### 1. Preparing Variables

Prepare variables that will store the list of hatch resources:

```cpp
long   fHatchResListID;
long   fHatchResListItemsCnt;
```

### 2. Creating the Resource List

Every time there is a change in the resources of this document, the list should be updated. **Note:** The list contains the default resources and also the resources of this type in THIS document.

```cpp
TXString     folderName;
fHatchResListItemsCnt = 0;
fHatchResListID = gSDK->BuildResourceList( kHatchDefNode,
                                           kDefaultHatchesFolder,
                                           folderName,
                                           fHatchResListItemsCnt );
```

### 3. Freeing the List

Free the list before destroying:

```cpp
::GS_DisposeResourceList( gCBP, fHatchResListID );
fHatchResListItemsCnt   = 0;
```

### 4. Filling the Popup with the Resource List

```cpp
VWThumbnailPopupCtrl*  pThumbnail  
            = this->GetThumbnailPopupCtrlByID( kThumbNailCtrl );
pThumbnail->RemoveAllImages();

for(long i=0; i<fHatchResListItemsCnt; i++) {
    pThumbnail->AddImageFromResource( fHatchResListID, i );
}
```

#### 4a. Ensuring Selection in the Popup

```cpp
VWThumbnailPopupCtrl*  pThumbnail 
                     = this->GetThumbnailPopupCtrlByID( kThumbNailCtrl );
pThumbnail->SelectItem( fThumbPatternIndex );
size_t selIndex = pThumbnail->GetObjectItemIndex( fThumbPatternIndex );
if ( selIndex == size_t(-1) && pThumbnail->GetItemsCount() > 0 ) {
    pThumbnail->SelectItem( size_t(0) );
}
```

### 5. Getting the Selection and Importing the Resource

Import the resource into the document if it does not exist:

```cpp
VWThumbnailPopupCtrl* pThumbnail = this->GetThumbnailPopupCtrlByID( kThumbNailCtrl );
InternalIndex popupIntIndex = pThumbnail->GetSelectedItem();
size_t popupIndex = pThumbnail->GetSelectedItemIndex();
InternalName name;
gSDK->InternalIndexToNameN( popupIntIndex, name );
 
if ( popupIndex != size_t(-1) && name[0] == 0 ) {
    MCObjectHandle  hResource = gSDK->ImportResourceToCurrentFile( fHatchResListID, popupIndex + 1 );

    ::GS_GetObjectName( gCBP, hResource, name );
    if ( ! gSDK->NameToInternalIndexN( name, popupIntIndex ) ) {
        popupIntIndex	= 0;
    }
}
 
fThumbPatternIndex	= - popupIntIndex;
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
