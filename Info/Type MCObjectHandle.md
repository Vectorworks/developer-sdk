# Type MCObjectHandle

## What is this?

This is a number (or 'void*') to an object inside VectorWorks.

This is the foundation element of the VectorWorks document architecture. Every object inside the VectorWorks document has a handle that is used to recognize this object from the others.

## Document Handle

The active document itself has an MCObjectHandle. `ISDK::GetDrawingHeader` will return you the active document handle.

## Object Type

Every handle represents an instance of an object with a specified type. You can get the object type by handle using `ISDK::GetObjectType` function.

For available type codes, see the Remarks section of `ISDK::GetObjectType`.

## Document Architecture

Some object types are containers. Through that object's MCObjectHandle, you can enumerate the child list.

See [The Vectorworks Environment](The%20Vectorworks%20Environment.md).

### Object Hierarchy Iteration

| Function                                                                 | Description                                                                 |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `ISDK::FirstMemberObj`              | Returns handle to the first child object of the specified one.              |
| `ISDK::LastMemberObj`               | Returns handle to the last child object of the specified one.               |
| `ISDK::ParentObject`                | Returns handle to the parent object of the specified one.                   |
| `ISDK::PrevObject`                  | Returns handle of the previous sibling object of the specified one.         |
| `ISDK::NextObject`                  | Returns handle of the next sibling object of the specified one.             |

### Hierarchy Manipulation

| Function                                                                 | Description                                                                 |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `ISDK::AddObjectToContainer` | Adds object instance into object container instance.                        |
| `ISDK::DeleteObject`         | Deletes the specified object from the hierarchy. The handle is no longer valid. |
| `ISDK::InsertObjectAfter`    | Repositions the specified object into the hierarchy, placing it after the specified one. The object is removed from its old place in the hierarchy. |
| `ISDK::InsertObjectBefore`   | Repositions the specified object into the hierarchy, placing it before the specified one. The object is removed from its old place in the hierarchy. |

## Aux List

Every object in VectorWorks has an Aux (Auxiliary) list of objects.

Every object can be put into another object's Aux list.

### Object Aux List Iteration

| Function                                                                 | Description                                                                 |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `ISDK::FirstAuxObject`          | Returns the previous sibling object in the Aux list of the specified one.   |
| `ISDK::NextAuxObject`           | Returns the next sibling object in the Aux list of the specified one.       |
| `ISDK::AuxOwner`                | Returns the object that contains the specified one in its Aux list.         |

### Object Aux List Manipulation

| Function                                                                 | Description                                                                 |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `ISDK::AttachAuxObject`        | Attaches an object to the specified object's Aux list.                      |
| `ISDK::DeleteAuxObject`        | Removes and deletes the specified object.                                   |
| `ISDK::RemoveAuxObject`        | Removes the specified object from the Aux list. Does not delete the object. |

### Object Aux List Tools

| Function                                                                 | Description                                                                 |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `ISDK::FindAuxObject`            | Searches for a particular object in the specified object's Aux list.        |
