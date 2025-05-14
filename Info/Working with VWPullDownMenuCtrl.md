# Working with VWPullDownMenuCtrl

## Initializing the Control

The initialization of the control is done in *OnSetUpEvent*:

```cpp
void CSampleDlg::OnSetUpEvent()
{
	VWDialog::OnSetUpEvent();

	VWPullDownMenuCtrl*  pPopupCtrl  = this->GetPullDownMenuCtrlByID( kMyListBox );

	pPopupCtrl->AddItem( "item1" );
	pPopupCtrl->AddItem( "item2" );
}
```

## Setting Markers to the Items

You can assign marker values to each element in the popup list. This helps manage dynamically changing popup lists.

The simplest example is making a popup list for enumerations, but having a dynamic subset of the enumeration as items in the popup list:

```cpp
enum EMyValues {
  kMyValues_One,
  kMyValues_Two,
  kMyValues_Three,
  kMyValues_Four,
};

pPopupCtrl->AddItem( "item1", size_t(-1), kMyValues_One );
pPopupCtrl->AddItem( "item3", size_t(-1), kMyValues_Three );

EMyValues myValue = ...;

// Select appropriate item
// Actually, the enumeration value is used
pPopupCtrl->SelectItemWithMarker( myValue );
```

Then, we could easily map backward from the selection index to the enumeration value:

```cpp
EMyValues myValue = (EMyValues) pPopupCtrl->GetSelectedItemMarker();
```

## Adding Separators

By adding a "-", a non-selectable separator is added to the list in the pull-down.

```cpp
pPopupCtrl->AddItem( "item1" );
pPopupCtrl->AddItem( "-" );
pPopupCtrl->AddItem( "item3" );
```

If you need to add a separator on a level in an enhanced pull-down, you need to add "- -". The first "-" is to create the "level", then a space (don't forget this one) followed by a second "-" to create the separator.

```cpp
pPopupCtrl->AddItemEnhanced(  "item1-sub1", "", 0 );
pPopupCtrl->AddItemEnhanced(  "item1- -", "", 1 );
pPopupCtrl->AddItemEnhanced(  "item1-sub2", "", 0 );
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
