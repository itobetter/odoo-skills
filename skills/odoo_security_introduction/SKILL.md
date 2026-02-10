---
name: Odoo Security Introduction
description: Configure Access Control Lists (ACLs) using CSV files.
---

# Odoo Security Introduction

## Goal
Allow users (specifically `base.group_user`) to access the `estate.property` model. Avoid the "The model has no access rules" warning.

## Access Control Lists (ACL)
Access rights are defined in `ir.model.access.csv` file, typically located in the `security/` folder.

**File Location:** `my_module/security/ir.model.access.csv`

### CSV Format
```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_estate_property,access_estate_property,model_estate_property,base.group_user,1,1,1,1
```

*   `id`: Unique XML ID for this access rule (e.g., `access_estate_property`).
*   `name`: Human-readable name (e.g., `access_estate_property`).
*   `model_id:id`: The model this rule applies to.
    *   Syntax: `model_` + `model_name` (where `.` is replaced by `_`).
    *   Example: `estate.property` -> `model_estate_property`.
*   `group_id:id`: The user group this rule applies to.
    *   `base.group_user`: Internal Users (Employees).
    *   `base.group_portal`: Portal Users.
    *   Leave empty for **Global** access (all users).
*   `perm_*`: 1 for allowed, 0 for denied.
    *   `read`: View records.
    *   `write`: Edit records.
    *   `create`: Create new records.
    *   `unlink`: Delete records.

## Manifest Configuration
You MUST register the CSV file in the `__manifest__.py` under the `data` key.

```python
'data': [
    'security/ir.model.access.csv',
],
```

## Tips
*   Access rights are loaded when the module is installed or updated.
*   Always define access rights for new models to prevent security warnings/errors.
