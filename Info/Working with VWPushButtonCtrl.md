## Handling event

The push event is handled by the event dispatch mechanism in the dialog class.  
First, you need to add handler functions below **DEFINE_EVENT_DISPATCH_MAP**:

```cpp
// dispatch map
protected:
    DEFINE_EVENT_DISPATCH_MAP;
    void		OnMyButton(long controlID, void* pData);
```

Then you provide the implementation of this function:

```cpp
CMyDialog::OnMyButton(long controlID, void* pdata)
{
	// button event handler
	// ...
}
```

At the end, you attach the function to the control:

```cpp
EVENT_DISPATCH_MAP_BEGIN(CMyDialog);
ADD_DISPATCH_EVENT(kMyButtonID, OnMyButton);
EVENT_DISPATCH_MAP_END;
```

## See Also

- [Creating handler class for VectorScript layout dialog](Dialog%20handler%20class%20for%20VectorScript%20layout%20dialog.md)
- [Creating handler class for manual layout dialog](Dialog%20handler%20class%20for%20manual%20layout%20dialog.md)
- [Working with Dynamic Data Exchange (DDX)](Dialog%20Dynamic%20Data%20Exchange%20(DDX).md)
