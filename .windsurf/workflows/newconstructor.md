---
description: Create a new constructor for an ABL class
---
# Create a New Constructor

This workflow helps you create either a default constructor or an overloaded constructor for an ABL class.

1. **Determine Constructor Type**
   - Ask: "Do you want to create a default constructor or an overloaded constructor?"
2. **For Default Constructor**
   - Create a simple constructor with no parameters
3. **For Overloaded Constructor**
   - Ask for parameters
4. **Analyze Class Properties**
   - Match parameters to properties and generate `THIS-OBJECT:PropertyName = parameterName.`
   - For each matched property, check whether it has a `SET` accessor. If it only has `GET.` (fully read-only), add `PRIVATE SET.` before inserting the constructor.
5. **Insert Constructor** before the first METHOD or END CLASS.

## Notes

- Always include SUPER() call at the beginning of the constructor
- Use THIS-OBJECT: prefix when assigning to properties
