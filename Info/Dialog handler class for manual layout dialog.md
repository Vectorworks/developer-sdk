# Dialog Handler

Manual dialogs are dialogs created by creating different controls and then manually adding them to the dialog. The first thing to do is creating your own class for the dialog inherited from the `VW::VWUI::VWDialog`.

See [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md) for VectorScript-defined dialogs. The difference is just the way the dialog is created. Actual event handling is the same.

Here is an example of what a dialog should look like:

```cpp
// The MyDialog.h file
class CMyDialog : public VW::VWUI::VWDialog
{
public:
                 CMyDialog();
    virtual      ~CMyDialog();

public:
    virtual bool  CreateDialogLayout();

    // events dispatcher map
protected:
    DEFINE_EVENT_DISPATH_MAP;
    void          EditIntegerChanged(long controlID, VWDialogEventArgs& eventArgs);

    virtual void  OnSetUpEvent();
    virtual void  OnDDXInitialize();
    virtual void  OnUpdateUI();

    // DDX variables
protected:
    long          fIntControlValue;

    // the controls
private:
    VW::VWUI::VWStaticTextCtrl     fStaticTextCtrl;
    VW::VWUI::VWEditIntegerCtrl    fEditIntegerCtrl;
};
```

```cpp
////////////////////////////////////////////////
// The MyDialog.cpp file
#include "MyDialog.h"
 
#define    kDialogID               128
#define    kTextControlID          3
#define    kEditIntegerControlID   4

using namespace VW::VWUI;
 
EVENT_DISPATCH_MAP_BEGIN( CMyDialog );
ADD_DISPATCH_EVENT( kEditIntegerControlID, EditIntegerChanged );
EVENT_DISPATCH_MAP_END;

CMyDialog::CMyDialog() :
    fStaticTextCtrl( kTextControlID ),
    fEditIntegerCtrl( kEditIntegerControlID )
{
    // TODO: Init DDX variables -- if any
    fIntControlValue    = 5;
}

CMyDialog::~CMyDialog()
{
}

bool CMyDialog::CreateDialogLayout()
{
    // create the dialog
    if ( ! this->CreateDilaog( "Dlg title", "OK", "Cancel", true ) )
        return false;

    // create the controls in the dialog
    if ( ! fStaticTextCtrl.CreateControl( this, "Enter a value:" ) )
        return false;
    if ( ! fEditIntegerCtrl.CreateControl( this, 4, 5 ) )
       return false;

    // add the controls to the dialog
    this->AddFirstGroupControl( & fStaticTextCtrl );
    this->AddRightControl( & fStaticTextCtrl, & fEditIntegerCtrl );
   
    return true;
}
 
void CMyDialog::OnSetUpEvent()
{
    VWDialog::OnSetUpEvent();

    // TODO: Add setup event handler
}

void CMyDialog::OnDDXInitialize()
{
    // TODO: Attach DDX variables -- if any
    this->AddDDX_EditInteger( kEditIntegerControlID, & fIntControlValue );
}

void CMyDialog::OnUpdateUI()
{
    VWDialog::OnUpdateUI();

    // TODO: Update user interface state
}

void CMyDialog::EditIntegerChanged(long controlID, VWDialogEventArgs& eventArgs)
{
    // TODO: Handle the control event
}
```

### Adding Initialization for the Controls of This Dialog

This is done in the dialog class' constructor by calling each control constructor with an appropriate ID. It's a good idea to move the control IDs as constants at the beginning of the file. 

You have to assign each control a unique ID for this dialog. 

**Note:** The control ID `1` is reserved for the default button, and control ID `2` is reserved for the cancel button. So you should use IDs from `3` up.

### Override `VWDialog::CreateDialogLayout` Function

This is the function that actually creates the dialog. Any user of this dialog will create an instance of the class and call this function to actually create the dialog and all controls inside. The function should return `true` if everything is okay with creation.

Before creating any controls, you should create the dialog itself. This is done by calling `VWDialog::CreateDilaog` function specifying the title of the dialog and text for the default button, usually "OK," and text for the cancel button, usually "Cancel."

**Note:** You can pass an empty string for the cancel button if you want your dialog to have only one button. Also, note that this is the place for creating controls; you should not put any initialization code.

Next, create each one of the controls you have in the dialog. Call each control's create method with appropriate parameters different for each control.

The last step is to add the created controls into the dialog. This is done by calling `VWDialog::AddFirstGroupControl`, `VWDialog::AddRightControl`, and `VWDialog::AddBelowControl`. First, assign the First Group Control for the dialog and then add the rest of the controls relative to the first one or relative to already added controls.

### Creating Dispatch Map

By using the `DEFINE_EVENT_DISPATH_MAP` macro in the class definition, you add the message dispatching mechanism to your dialog. You also have to implement the map in your `.cpp` file with the macros: `EVENT_DISPATCH_MAP_BEGIN` and `EVENT_DISPATCH_MAP_END`. Within these two, you should add dispatcher entries, specifying which message ID maps to which function.

### Add Dispatch Event for the Controls

By using the `ADD_DISPATCH_EVENT` macro, you specify which function should be called when an event for a specified control ID appears. 

In the example above, a function is assigned to the event for the integer control, and that is the event when the control was changed.

### Override `OnSetUpEvent` Function

This function is called when the dialog is already created but not shown. This is the place where you **should** initialize your control content, hide controls, and so on.

### Override `VWDialog::OnUpdateUI` Function

This function is called when the dialog should be updated. That happens whenever a control in the dialog layout is changed.

**Note:** This function should **only** be used to enable/disable and hide/show controls on the dialog. You should not use it for anything else.

### Override `VWDialog::OnDDXInitialize` Function

This function is part of the [Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md) mechanism. This mechanism provides you with a simple way of putting values into the controls and getting them out of there.

This function is called before the dialog is shown, and it should bind the concrete variables of the class with the controls in the dialog.

### Running the Dialog

```cpp
CMyDialog dlg;
if ( dlg.RunDialogLayout() == VW::VWUI::kDialogButton_Ok ) {
    // TODO: Do something useful
}
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
- [Using Saved Settings](Dialog%20Using%20Saved%20Settings.md)

