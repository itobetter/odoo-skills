---
name: Odoo Models and Basic Fields
description: Define Odoo models, model attributes, and basic field types.
---

# Odoo Models and Basic Fields

## Goal
Create the `estate.property` model with fields like name, description, price, etc.

## 1. Defining a Model
Models are Python classes inheriting from `models.Model`.
File path: `my_module/models/my_model.py`

**Example:**
```python
from odoo import fields, models

class EstateProperty(models.Model):
    _name = "estate.property"
    _description = "Real Estate Plans"

    name = fields.Char(required=True)
    description = fields.Text()
```

### Key Attributes
*   `_name` (required): Technical name of the model (e.g., `estate.property`). Use dots `.` for namespacing.
*   `_description`: User-friendly name/description of the model.

## 2. Basic Field Types
Odoo provides various field types in `odoo.fields`.

| Field Type | Description | Python Equivalent | SQL Equivalent |
| :--- | :--- | :--- | :--- |
| `Char` | Short text (string) | `str` | `VARCHAR` |
| `Text` | Long multiline text | `str` | `TEXT` |
| `Integer` | Whole numbers | `int` | `INTEGER` |
| `Float` | Decimal numbers | `float` | `FLOAT` |
| `Boolean` | True/False | `bool` | `BOOLEAN` |
| `Date` | Date (YYYY-MM-DD) | `datetime.date` | `DATE` |
| `Datetime` | Date and Time | `datetime.datetime` | `TIMESTAMP` |
| `Selection`| Dropdown choice | `str` | `VARCHAR` |

### Selection Field Example
```python
garden_orientation = fields.Selection(
    string='Orientation',
    selection=[('north', 'North'), ('south', 'South')],
    help="Orientation of the garden"
)
```

## 3. Common Field Attributes
*   `string`: Label of the field in the UI (default is capitalized field name).
*   `required`: If `True`, the field cannot be empty.
*   `readonly`: If `True`, the user cannot edit the field.
*   `copy`: If `False`, the field is not copied when duplicating a record.
*   `default`: Default value (value or callable).
*   `help`: Tooltip for the field.

## 4. Updates & SQL
*   When changing the Python model definition, restart Odoo with `-u <module_name>` to trigger a database schema update.
*   Command: `odoo-bin -u estate -d my_db`

## 5. Automatic Fields
Odoo automatically adds these fields to every model:
*   `id`: Unique ID (Primary Key).
*   `create_date`, `create_uid`: Creation timestamp and user.
*   `write_date`, `write_uid`: Last modification timestamp and user.
