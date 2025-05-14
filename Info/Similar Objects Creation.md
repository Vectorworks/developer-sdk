By [Vladislav Stanev](mailto:vstanev@nemetschek.net)

## What's that

Make the menu command **Create Similar Object** work for any custom parametric.

## Parametric Object Part

The parametric object responds to an event with the name of the tool that should be activated. The name of the tool is actually the name of the dynamic library of the tool plug-in.

In your parametric plug-in, you should handle the **kObjOnGetToolName** event inside your **Plug-in Object Main Function**.

```cpp
// filepath: ParametricMain.cpp
long ParametricMain(long action, MCObjectHandle hParametricObj, long message, long &userData)
{
  // ...existing code...
  long reply = kParametricNoErr;
  switch (action) {

    // ...existing code...

    case kObjOnGetToolName: {
      TXString* pToolName = (TXString*) message;
      if (pToolName) {
         // return the dyn library file name of the tool plug-in
         *pToolName = "SampleObjectTool";
         reply = kObjectEventHandled;
      }
      break;

    // ...existing code...

  }

  return reply;
}
```

## Tool Part

The tool is notified by VectorWorks for the object that needs to be duplicated.

In your tool plug-in, you should handle the **kToolInitByObject** event inside your **Plug-in Tool Main Function**.

The tool can set itself up properly using the specified object handle.

**Note:** VectorWorks has already copied the parametric record data.

```cpp
// filepath: ToolMain.cpp
long ToolMain(long action, long message1, long message2, long &userData)
{
  // ...existing code...

  switch (action) {

    // ...existing code...

    case kToolInitByObject: {
      MCObjectHandle hTheObject = (MCObjectHandle) message1;

      // use hTheObject to set proper default data in your tool.
      // NOTE: VectorWorks has already copied the parametric record data
      // ...

      break;
    }

    // ...existing code...
  }
}
```
