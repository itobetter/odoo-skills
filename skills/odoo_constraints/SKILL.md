---
name: Odoo Constraints
description: Ensure data consistency using SQL Constraints and Python Constraints.
---

# Odoo Constraints

## Goal
Prevent invalid data from being saved to the database.

## 1. SQL Constraints
Enforced by the PostgreSQL database. Efficient and atomic.
*   Defined via `_sql_constraints` model attribute.
*   List of tuples: `('constraint_name', 'SQL CHECK/UNIQUE', 'Error Message')`.

**Example:**
```python
class EstateProperty(models.Model):
    _name = "estate.property"

    _sql_constraints = [
        ('check_expected_price', 'CHECK(expected_price > 0)', 'The expected price must be positive.'),
        ('check_selling_price', 'CHECK(selling_price >= 0)', 'The selling price must be positive.'),
        ('name_uniq', 'UNIQUE(name)', 'Property name must be unique!'),
    ]
```
*   **Note:** If existing data violates the constraint, the module update/install will fail. You must fix the data first.

## 2. Python Constraints
Enforced by Odoo server application logic. Used for complex validations that SQL cannot handle easily.
*   **Decorator:** `@api.constrains('field1', 'field2')`
*   **Exception:** Raise `odoo.exceptions.ValidationError` if check fails.

**Example:**
```python
from odoo.exceptions import ValidationError
from odoo.tools.float_utils import float_compare

@api.constrains('selling_price', 'expected_price')
def _check_selling_price(self):
    for record in self:
        # Skip check if no selling price is set
        if float_is_zero(record.selling_price, precision_digits=2):
            continue

        # Check if selling price is lower than 90% of expected
        if float_compare(record.selling_price, record.expected_price * 0.9, precision_digits=2) == -1:
            raise ValidationError("Selling price cannot be lower than 90% of the expected price.")
```
*   **Float Comparison:** ALWAYS use `float_compare` and `float_is_zero` when working with monetary fields to avoid rounding errors.
*   **Trigger:** The method runs whenever any of the fields in `@api.constrains` are modified.

## 3. SQL vs Python
*   **SQL:** Faster, strictly enforced at DB level. Use for simple checks (ranges, uniqueness).
*   **Python:** Slower, enforced at app level. Use for complex logic involving multiple records or python-specific calculations.
