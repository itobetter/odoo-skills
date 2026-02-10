---
name: Odoo Computed Fields and Onchanges
description: Define computed fields, dependencies, inverses, and onchange methods.
---

# Odoo Computed Fields and Onchanges

## Goal
Implement business logic: Compute Total Area, Best Offer Price, and handle UI updates when fields change.

## 1. Computed Fields
Fields whose values are calculated via a Python method rather than stored directly (by default).
*   **Attribute:** `compute="method_name"`
*   **Decorator:** `@api.depends('field1', 'field2.subfield')`

**Example:**
```python
from odoo import api, fields, models

class EstateProperty(models.Model):
    # ...
    total_area = fields.Float(compute="_compute_total_area")
    living_area = fields.Integer()
    garden_area = fields.Integer()

    @api.depends("living_area", "garden_area")
    def _compute_total_area(self):
        for record in self:
            record.total_area = record.living_area + record.garden_area
```
*   **Critical:** Always iterate over `self` because `self` is a recordset (can contain 1 or N records).
*   **Dependencies:** The method is triggered when dependency fields change.

## 2. Inverse Methods
Make a computed field editable by defining an `inverse` method to set the source fields.
*   **Attribute:** `inverse="set_method_name"`

**Example:**
```python
validity = fields.Integer(default=7)
date_deadline = fields.Date(compute="_compute_date_deadline", inverse="_inverse_date_deadline")

@api.depends("validity", "create_date")
def _compute_date_deadline(self):
    for record in self:
        # Logical implementation...
        pass

def _inverse_date_deadline(self):
    for record in self:
        # Update 'validity' based on 'date_deadline'
        pass
```

## 3. Onchanges (`@api.onchange`)
Updates the UI **immediately** when a user modifies a field, WITHOUT saving to the database.
*   **Scope:** Client-side dynamic behavior (e.g., auto-filling fields).
*   **Decorator:** `@api.onchange('field_name')`

**Example:**
```python
@api.onchange("garden")
def _onchange_garden(self):
    if self.garden:
        self.garden_area = 10
        self.garden_orientation = "north"
    else:
        self.garden_area = 0
        self.garden_orientation = False
```
*   **Note:** Onchanges work on the form view record (`self` is typically a single record in this context, but treating it as a recordset is safe). Warning: Values are NOT saved until the user clicks Save.
*   Use `onchange` for UI assistance; use `compute` for data consistency.

## 4. Stored Computed Fields
By default, computed fields are not stored in the DB (cannot be searched).
*   Add `store=True` to store the value.
*   The value is recomputed and updated in the DB whenever dependencies change.
