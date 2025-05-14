By [François de Felix](mailto:defelix@cesyam.fr)

## What's that

This article will explain how to create a multiple extrude using the SDK.

![Multiple Extrude](images/multiple_extrude.jpg)

To create an empty multiple extrude object, use:
```cpp
gSDK->Kludge( 3800, doubleParams, &multiExtrude );
```

- `doubleParams` is a pointer to an array of 2 double_params (bottom and top of the extrude).
- `multiExtrude` is a handle on the resulting multiple extrude object.

## Example

```cpp
MCObjectHandle CreateMultiExtrude(MCObjectHandle obj1, MCObjectHandle obj2, WorldCoord bottom, WorldCoord top)
{
    MCObjectHandle multiExtrude;
    double doubleParams[] = {bottom, top};
    gSDK->Kludge( 3800, doubleParams, &multiExtrude );
         // now multiExtrude is a handle on my new empty multiple extrude

    gSDK->AddObjectToContainer(obj1, multiExtrude );
    gSDK->AddObjectToContainer(obj2, multiExtrude );
             // you can add more than 2 objects in your extrude

    gSDK->ResetObject(multiExtrude);
    
    return multiExtrude;
}
