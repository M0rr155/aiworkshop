# ABL Business Entity Architecture Pattern

## Overview

The Business Entity pattern provides a standardized, maintainable approach to data access in OpenEdge ABL applications. It separates UI logic from database operations through a layered architecture that promotes reusability, testability, and consistency across the application.

## Architecture Layers

### 1. UI Layer (Windows/Forms)
- **Responsibility**: User interaction and presentation
- **Access**: Never directly accesses database tables
- **Communication**: Calls Business Entity methods with datasets

### 2. Business Entity Layer
- **Responsibility**: Data access, business rules, validation
- **Inheritance**: Extends `OpenEdge.BusinessLogic.BusinessEntity`
- **Management**: Instantiated through EntityFactory (singleton pattern)

### 3. Database Layer
- **Responsibility**: Persistent storage
- **Access**: Only through data-sources attached to business entities

## Key Components

### EntityFactory (Singleton Pattern)

```abl
CLASS business.EntityFactory:
    VAR PRIVATE STATIC EntityFactory objInstance.
    VAR PRIVATE CustomerEntity objCustomerEntityInstance.

    CONSTRUCTOR PRIVATE EntityFactory():
    END CONSTRUCTOR.

    METHOD PUBLIC STATIC EntityFactory GetInstance():
        IF objInstance = ? THEN
            objInstance = NEW EntityFactory().
        RETURN objInstance.
    END METHOD.

    METHOD PUBLIC CustomerEntity GetCustomerEntity():
        IF objCustomerEntityInstance = ? THEN
            objCustomerEntityInstance = NEW CustomerEntity().
        RETURN objCustomerEntityInstance.
    END METHOD.
END CLASS.
```

### Dataset Definition (.i Include Files)

```abl
DEFINE TEMP-TABLE ttCustomer BEFORE-TABLE bttCustomer
    FIELD CustNum AS INTEGER INITIAL "0" LABEL "Cust Num"
    FIELD Name AS CHARACTER LABEL "Name"
    INDEX CustNum IS PRIMARY UNIQUE CustNum ASCENDING.

DEFINE DATASET dsCustomer FOR ttCustomer.
```

### Business Entity Class

```abl
CLASS business.CustomerEntity INHERITS BusinessEntity USE-WIDGET-POOL:
    {business/CustomerDataset.i}
    DEFINE DATA-SOURCE srcCustomer FOR Customer.

    CONSTRUCTOR PUBLIC CustomerEntity():
        SUPER(DATASET dsCustomer:HANDLE).
        VAR HANDLE[1] hDataSourceArray = DATA-SOURCE srcCustomer:HANDLE.
        VAR CHARACTER[1] cSkipListArray = [""].
        THIS-OBJECT:ProDataSource = hDataSourceArray.
        THIS-OBJECT:SkipList = cSkipListArray.
    END CONSTRUCTOR.
END CLASS.
```

## Standard CRUD Operations

### Read
```abl
METHOD PUBLIC LOGICAL GetCustomerByNumber(INPUT ipiCustNum AS INTEGER,
                                          OUTPUT DATASET dsCustomer):
    cFilter = "WHERE Customer.CustNum = " + STRING(ipiCustNum).
    THIS-OBJECT:ReadData(cFilter).
    RETURN CAN-FIND(FIRST ttCustomer).
END METHOD.
```

### Update (with change tracking)
```abl
TEMP-TABLE ttCustomer:TRACKING-CHANGES = TRUE.
ttCustomer.Name = "Updated Name".
objEntity:UpdateCustomer(INPUT-OUTPUT DATASET dsCustomer BY-REFERENCE).
```

### Validate before persist
```abl
isValid = objEntity:ValidateCustomer(DATASET dsCustomer BY-REFERENCE, OUTPUT cErrorMessage).
IF isValid THEN
    objEntity:UpdateCustomer(DATASET dsCustomer BY-REFERENCE).
ELSE
    MESSAGE cErrorMessage VIEW-AS ALERT-BOX.
```

## Refactoring Checklist

- [ ] Create `src/business/XxxDataset.i` with explicit fields and `BEFORE-TABLE`
- [ ] Create `src/business/XxxEntity.cls` inheriting `BusinessEntity`
- [ ] Wire data source in constructor (`ProDataSource`, `SkipList`)
- [ ] Add `GetXxxBy...` methods (filter string → `ReadData` → `CAN-FIND`)
- [ ] Add `CreateXxx`, `UpdateXxx`, `DeleteXxx` delegating to base class
- [ ] Add `ValidateXxx` returning LOGICAL + error message
- [ ] Add `GetXxxEntity()` to `EntityFactory` with lazy singleton
- [ ] Update UI: use `EntityFactory:GetInstance():GetXxxEntity()`
- [ ] Enable `TRACKING-CHANGES` before modifying temp-table records
- [ ] Remove direct `FOR EACH` / `FIND` database access from UI

## Common Pitfalls

- Use `OUTPUT DATASET` (not `BY-REFERENCE`) for read operations
- Always enable `TRACKING-CHANGES` before updating
- Assign `ProDataSource` and `SkipList` in constructor
- Never access database directly from UI layer
- Always use named method-scoped buffers for database access
