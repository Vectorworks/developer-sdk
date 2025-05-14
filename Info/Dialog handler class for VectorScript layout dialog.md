# Dialog Handler

The first thing to do is to create a [VectorScript](https://github.com/Vectorworks/developer-scripting) that creates the desired dialog. The best way to do this is to use [Dialog Builder](Dialog%20Builder.md).

Then you should put the text of the script in the `DialogLayout` section in your resource file (vwr file) and give it a unique name for the resource file.

The only difference for this dialog from the [manual layout dialogs](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md) is that you create the dialog not control by control, but by telling Vectorworks to call the script which creates the dialog.

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
    void          EditIntegerChanged(long controlID, VWDialogEventArgs eventArg);

    virtual void  OnSetUpEvent();
    virtual void  OnDDXInitialize();
    virtual void  OnUpdateUI();

// DDX variables
protected:
    long          fIntControlValue;

// Inner data
private:

};
```

```cpp
// The MyDialog.cpp file
#include "MyDialog.h"
  
#define    kTextControlID          3
#define    kEditIntegerControlID   4

using namespace VW::VWUI; 

EVENT_DISPATCH_MAP_BEGIN( CMyDialog );
ADD_DISPATCH_EVENT( kEditIntegerControlID, EditIntegerChanged );
EVENT_DISPATCH_MAP_END;

CMyDialog::CMyDialog()
{
     // TODO: Init DDX variables -- if any
}

CMyDialog::~CMyDialog()
{
}

bool CMyDialog::CreateDialogLayout()
{
    return this->CreateDialogLayoutFromVWR( "ResourceFile/DialogLayout/Layout.vs" );
}

void CMyDialog::OnSetUpEvent()
{
    VWDialog::OnSetUpEvent();
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

void CMyDialog::EditIntegerChanged(long controlID, VWDialogEventArgs eventArg)
{
    // TODO: Handle the control event
}
```

### Override **VWDialog::CreateDialogLayout** function
- Call **VWDialog::CreateDialogLayoutFromVWR** function.
- This is the function which actually creates the dialog. Any user of this dialog will create an instance of the class and call this function to actually create the dialog and all controls inside. The function should return `true` if everything is okay with creation.
- Here you tell VectorWorks to create the dialog from the `DialogLayout` resource by passing the name of the resource. In this example, `"ResourceFile/DialogLayout/Layout.vs"` is the path of the VS resource containing the VectorScript creating the dialog.

### Creating Dispatch Map
- By using **DEFINE_EVENT_DISPATH_MAP** macros in the class definition, you add the message dispatching mechanism to your dialog. You also have to implement the map in your `.cpp` file with the macros: **EVENT_DISPATCH_MAP_BEGIN** and **EVENT_DISPATCH_MAP_END**. Within these two, you should add dispatcher entries, specifying which message ID and which function should be called.

### Add Dispatch Event for the Controls
- By using **ADD_DISPATCH_EVENT** macros, you specify which function should be called when an event for a specified control ID appears.
- In the example above, a function is assigned to the event for the integer control, and that is the event when the control was changed.

### Override **VWDialog::OnSetUpEvent** function (or `OnInitializeContent()`)
- This function is called when the dialog is already created but not shown. This is the place where you **should** initialize your control content, hide controls, and so on.

### Override **VWDialog::OnUpdateUI** function
- This function is called when the dialog should be updated. That happens when any control in the dialog layout is changed.
- This is the place where you normally enable/disable or show/hide controls in the dialog layout.

### Override **VWDialog::OnDDXInitialize** function
- This function is part of the [Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md) mechanism. This mechanism provides you a simple way of putting values into the controls and getting them out of there.
- This function is called before the dialog is shown, and it should bind the concrete variables of the class with the controls in the dialog.

### Running the Dialog

```cpp
CMyDialog	dlg;
if ( dlg.RunDialogLayout() == VW::VWUI::kDialogButton_Ok ) {
   // TODO: Do something useful
}
```

## See Also

- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
- [Using Saved Settings](Dialog%20Using%20Saved%20Settings.md)
