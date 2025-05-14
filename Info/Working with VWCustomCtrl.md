# Setup Custom Control in VWDialog

You need to create a class extending `VWCustomCtrl`:

```cpp
class CMyControl : public VWCustomCtrl
{
public:
                CMyControl(TControlID id);
  virtual       ~CMyControl();

  // eventing
protected:
  virtual void  OnControlAttached();
  virtual void  OnPaint(VWCustomCtrlDC& dc);

  // ...
};
```

Then you need to create an instance of your custom control in the `VWDialog`, and attach it to receive the events through DDX:

```cpp
class CSampleDlg4 : public VWDialog
{
  // ...

  // DDX
protected:
  fMyCustomCtrl1;

  // ...
};
```

```cpp
CSampleDlg4::CSampleDlg4() :
    fMyCustomCtrl1( kMyCustomCtrlID ),
{
    // ...
}
```

```cpp
void CSampleDlg4::OnDDXInitialize()
{
  this->AddDDX_Control( & fMyCustomCtrl1 );
  // ...
}
```

## Using Tooltips

To use the tooltips, you need to enable them and initialize the tooltip regions.

You should enable them after the control is created. This means in `OnControlAttached` or subsequent events. By default, tooltips are disabled.

You need to define tooltips to specify information for them. Tooltips are basically a definition of the following struct:

```cpp
struct SCustomCtrlToolTip
{
  TXString   fText;
  ViewRect   fRect;
  bool       fbTrackMouse;
  ViewPt     fPos;
};
```

- **`fText`**: The text that will appear when the tooltip is shown.
- **`fRect`**: Active region for the tooltip. When the cursor enters this area, it will show the tooltip. The rectangle is in local coordinates of the custom control. By default, it is set to empty, which means that this tooltip is for the entire region of the custom control.
- **`fbTrackMouse`**: `true` if you want the tooltip text to follow the mouse; `false` if the tooltip will stay where it appears for the first time.
- **`fPos`**: Specifies the custom position of the tooltip; it will stay at this position as long as `fbTrackMouse` is `false`.

```cpp
void CMyControl::OnControlAttached()
{
  this->EnableToolTips( true );

  SCustomCtrlToolTip	toolTip1;
  toolTip1.fText			= "this is my tool tip";
  toolTip1.fPos.Set( 10, 10 );
  this->AddToolTip( toolTip1 );

  SCustomCtrlToolTip	toolTip2;
  toolTip2.fText			= "region tool tip";
  toolTip2.fRect			= ViewRect( 15, 15, 55, 55 );
  toolTip2.fbTrackMouse	= true;
  this->AddToolTip( toolTip2 );

  SCustomCtrlToolTip	toolTip3;
  toolTip3.fText			= "another region tool tip";
  toolTip3.fRect			= ViewRect( 55, 15, 105, 55 );
  this->AddToolTip( toolTip3 );
}
```

There should be only one tooltip that has no `fRect` defined.

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
