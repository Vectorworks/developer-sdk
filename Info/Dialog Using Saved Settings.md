# Set up the saved settings mechanism

The first thing to do is to assign a category for the saved settings dialog.

This is done in the dialog constructor, and the name provided is usually the name of the plug-in. This way all saved settings for this plug-in will be present under the same node in the settings XML file.

There are two available functions for setting the category:

```cpp
void VWDialog::SetSavedSettingsTag(const TXString& category);
```

```cpp
void VWDialog::SetSavedSettingsTag(const TXString& category,
                                   const TXString& dialogName);
```

The **dialogName** specifies an additional category inside the original **category** in which this dialog's saved settings will be stored.

If the function without **dialogName** is used, the subcategory is actually the title of the dialog.

**Note!** If you omit specifying **dialogName**, the dialog title is used as the subcategory. This means that in localized dialogs, the localized name will be used. This is okay in the general case, as far as it concerns user-only settings files.

```cpp
CSampleDlg1::CSampleDlg1()
{
    // ...

    // Specify category in which the dialog's saved settings will be stored
    this->SetSavedSettingsTag("SampleCategory");
}
```

## Using dialog saved settings

In order for the mechanism to work properly, you **MUST** call `VWDialog::OnSetUpEvent();` if you have overridden this function.

```cpp
void CSampleDlg1::OnSetUpEvent()
{
    VWDialog::OnSetUpEvent();
}
```

### Saving dialog position and size

If you have used **SetSavedSettingsTag** in your dialog, then it will automatically store its position and size on dialog exit, and it will restore them when the dialog is shown.

**Note!** Saving will be done regardless of whether the dialog finishes with OK or Cancel.

### Using custom saved settings values

The `VWDialog` has a built-in mechanism for custom values (additional data) for this dialog that will go in the dialog's category and subcategory. The functions to be used are:

```cpp
void VWDialog::SetSavedValue(const TXString& name,
                             const TXString& value);
```

```cpp
bool VWDialog::GetSavedValue(const TXString& name, TXString& outValue) const;
```

The first thing to do is to provide the available value names before the dialog is run:

```cpp
CSampleDlg1::CSampleDlg1()
{
    // ...

    // Specify category in which the dialog's saved settings will be stored
    this->SetSavedSettingsTag("SampleCategory");
    this->SetSavedValue("myCustValue", "1");
    this->SetSavedValue("myCustValueName", "this is name");
}
```

This will set default values for the custom saved settings.

Now you can use those value names whenever in the dialog handler code:

```cpp
void CSampleDlg1::OnGetDataButton(long controlID,
                                  VWDialogEventArgs& eventArgs)
{
    TXString value;
    this->GetSavedValue("myCustValue", value);

    // ...
}
```

Also, you can set new values whenever in the code you like:

```cpp
void CSampleDlg1::OnGetDataButton(long controlID,
                                  VWDialogEventArgs& eventArgs)
{
    this->SetSavedValue("myCustValue", "this is the new value");

    // ...
}
```

**Note!** All custom values are strings. You can use `TXString` conversion functions: `TXString::Format`, `TXString::itoa`, `TXString::atoi`, and `TXString::atof`.

**Note!** If you are using `VWDialog::SetSavedValue` in `VWDialog::OnSetDownEvent`, you **HAVE TO** do it before calling the base `VWDialog` function:

```cpp
void CSampleDlg1::OnSetDownEvent()
{
    // Save before calling the base,
    // because this base function actually writes the custom values
    this->SetSavedValue("myCustValue", "this is the new value");

    VWDialog::OnSetDownEvent();
}
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)

