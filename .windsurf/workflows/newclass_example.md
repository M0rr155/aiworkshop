---
description: Create a new ABL class
---
# Create a New ABL Class

This workflow guides you through creating a new ABL class with proper structure.

1. **Determine Class Name** and interface implementation.
   - Choose a descriptive name for your class
2. **Select Package Location**
   - Decide which package/directory the class should be placed in
3. **Choose Inheritance**
   - Determine which class to inherit from
4. **Select Interfaces to Implement**
5. **Generate Class Structure**

## Example Class Structure

```abl
USING Progress.Lang.*.
USING OpenEdge.BusinessLogic.BusinessEntity.

BLOCK-LEVEL ON ERROR UNDO, THROW.

CLASS package.MyNewClass INHERITS BusinessEntity USE-WIDGET-POOL:

    CONSTRUCTOR PUBLIC MyNewClass():
    END CONSTRUCTOR.

    METHOD PUBLIC VOID ExampleMethod():
    END METHOD.

END CLASS.
```

## Best Practices

- Always use the USE-WIDGET-POOL option when defining a class
- Always reference database tables using a locally defined named buffer
