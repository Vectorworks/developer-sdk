# Working with Dynamic Data Exchange (DDX)

Dynamic Data Exchange (DDX) is meant to simplify the way the data for the controls is kept up to date. The idea is that you have a variable for each control. Automatically, when the control's data changes, the value of this variable is updated. There is also `VWDialog::UpdateData` function that you can call to update in both directions -- from the controls to the variables and vice versa.

## Steps to Attach a Variable to a Control

### Add a Variable Definition

In your dialog definition, the type of the variable depends on the type of the control. Look at the end of the document for the tables of types for each control variable.

```cpp
// variable for DDX
long     fIntControlValue;
```

### Attach the Variable to the Control

The control is identified by its integer ID. It's a good idea to define your control identifiers as constants. The attaching is done in the `VWDialog::OnDDXInitialize` function after doing everything else, such as initializing and attaching controls if you are creating a dynamic dialog. For adding, see the DDX-related functions in `VWDialog` since each control type has a different function that attaches the DDX variable. 

The value of the variable is put in the control when the variable is attached to the control. Look at the end of the document for the tables of types for each control variable.

```cpp
// add DDX variables to the control
this->AddDDX_EditInteger( kEditIntegerControlID, & fIntControlValue );
```

Now the dialog ensures that the DDX variables are updated with the control's data.

In event functions (either virtual or event handler functions), you always have proper data in the variables for all the controls. If you want to update the variables and put the new value in the controls explicitly, you can use the `VWDialog::UpdateData` function by passing `true`, which will cause the function to update the controls with their DDX variables. The variables are automatically updated before a function is called and after it finishes.

```cpp
fIntControlValue   = 11;
this->UpdateData( true );
```

## Supported Types for the DDX Mechanism

The table below describes all supported types for the DDX mechanism. It also shows the classes from the `VectorWorks::VWUI` namespace that provide an interface for working with specific controls. Additionally, it shows what type is expected to be the DDX variable for each control type.

| Control                              | Type      | DDX Function                                      |
|--------------------------------------|-----------|--------------------------------------------------|
| VWCheckButtonCtrl   | `bool`    | VWDialog::AddDDX_CheckButton |
| VWCheckGroupBoxCtrl | `bool`    | VWDialog::AddDDX_CheckGroupBox |
| VWColorButtonCtrl   | `RGBColor`| VWDialog::AddDDX_ColorButton |
| VWEditIntegerCtrl)  | `long`    | VWDialog::AddDDX_EditInteger |
| VWEditRealCtrl      | `double`  | VWDialog::AddDDX_EditReal |
| VWEditTextCtrl      | `TXString`| VWDialog::AddDDX_EditText |
| VWRadioButtonCtrl   | `bool`    | VWDialog::AddDDX_RadioButton |
| VWRadioGroupBoxCtrl | `bool`    | VWDialog::AddDDX_RadioGroupBox |
| VWPullDownMenuCtrl  | `short`   | VWDialog::AddDDX_PulldownMenu |
| VWListBoxCtrl       | `short`   | VWDialog::AddDDX_ListBox |

## See Also

- [Creating handler class for VectorScript layout dialog](Creating handler class for VectorScript layout dialog)
- [Creating handler class for manual layout dialog](Creating handler class for manual layout dialog)
- [Using Saved Settings](Using Saved Settings)
