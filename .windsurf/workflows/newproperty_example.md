---
description: Add a new property to an existing class
---
# New Property Addition Workflow

This workflow adds a new property to the currently open ABL class.

1. **Property Name**
2. **Property Type** - CHARACTER, INTEGER, DECIMAL, LOGICAL, DATE, etc.
3. **GET access level** - PUBLIC, PROTECTED, PRIVATE
4. **SET access level** - PUBLIC, PROTECTED, PRIVATE, or NONE (read-only)
5. **Static?** - YES or NO

## Simple Property Pattern

```abl
DEFINE PUBLIC PROPERTY PropertyName AS CHARACTER NO-UNDO
    GET.
    PRIVATE SET.
```

## Best Practices

- Use private setters for properties modified only internally
- Add `PRIVATE SET.` when a constructor needs to assign a read-only property
