# Working with VWListBoxCtrl

## Initializing the Control

The initialization of the control is done in *OnSetUpEvent*:

```cpp
void CSampleDlg::OnSetUpEvent()
{
	VWDialog::OnSetUpEvent();

	VWListBoxCtrl*  pListBox  = this->GetListBoxCtrlByID( kMyListBox );

	pListBox->AddItem( "item1" );
	pListBox->AddItem( "item2" );
}
```

## Setting Markers to the Items

You can assign marker values to each element in the list. This helps manage dynamically changing lists.

The simplest example is making a list for enumerations, but having a dynamic subset of the enumeration as items in the list:

```cpp
enum EMyValues {
  kMyValues_One,
  kMyValues_Two,
  kMyValues_Three,
  kMyValues_Four,
};

pListBox->AddItem( "item1", size_t(-1), kMyValues_One );
pListBox->AddItem( "item3", size_t(-1), kMyValues_Three );

EMyValues myValue = ...;

// Select the appropriate item
// Actually, the enumeration value is used
pListBox->SelectItemWithMarker( myValue );
```

Then, we could easily map backward from the selection index to the enumeration value:

```cpp
EMyValues myValue = (EMyValues) pListBox->GetSelectedItemMarker();
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
