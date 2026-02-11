---
name: Odoo ORM API
description: Master the Odoo ORM for data manipulation, environment management, and CRUD operations.
---

# Odoo ORM API

## Goal
Manipulate data programmatically using the Odoo Object-Relational Mapping (ORM) API.

## 1. The Environment (`self.env`)
The environment stores the context, the cursor, and the user.
*   `self.env.user`: The current user record.
*   `self.env.company`: The current company record.
*   `self.env.context`: A dictionary containing session data (lang, timezone, etc.).
*   `self.env.ref(xml_id)`: Get a record by its XML ID.

### Modifying the Environment
Methods in Odoo models are executed with the current user's privileges. To change this:
*   `sudo()`: Switch to the Superuser (bypass security rules).
    ```python
    self.env['res.partner'].sudo().create({'name': 'Admin Contact'})
    ```
*   `with_context(**kwargs)`: Add or modify context keys.
    ```python
    self.with_context(lang='fr_FR').name  # Translates name to French
    ```
*   `with_company(company)`: Switch the active company.

## 2. Searching Records
*   `search(domain, limit=None, offset=0, order=None)`: Returns a recordset.
    ```python
    properties = self.env['estate.property'].search([
        ('state', '=', 'new'),
        ('expected_price', '<', 100000)
    ], limit=10)
    ```
*   `search_count(domain)`: Returns the number of records (integer).
*   `browse(ids)`: Returns a recordset from a list of IDs.
    ```python
    property = self.env['estate.property'].browse([1, 2, 3])
    ```
*   **Domain**: A list of tuples `(field, operator, value)`.
    *   Operators: `=`, `!=`, `>`, `>=`, `<`, `<=`, `like`, `ilike`, `in`, `not in`.
    *   Logical: `&` (AND, default), `|` (OR), `!` (NOT).

## 3. CRUD Operations
*   `create(vals_list)`: Create new records.
    ```python
    # Single record
    new_prop = self.env['estate.property'].create({'name': 'New House', 'expected_price': 50000})
    # Multiple records (faster)
    props = self.env['estate.property'].create([{'name': 'H1'}, {'name': 'H2'}])
    ```
*   `write(vals)`: Update records.
    ```python
    # Updates ALL records in the recordset 'properties'
    properties.write({'state': 'offer_received'})
    ```
*   `unlink()`: Delete records.
    ```python
    properties.unlink()
    ```

## 4. Exceptions
Use Odoo exceptions to stop execution and warn the user.
```python
from odoo.exceptions import UserError, ValidationError

# Validations
if record.selling_price < record.expected_price * 0.9:
    raise ValidationError("Selling price cannot be lower than 90% of expected price.")

# User Warnings
if not record.partner_id:
    raise UserError("You must select a partner first.")
```

## 5. Mapped and Filtered
Optimization tools for recordsets.
*   `mapped(field_name)`: Returns a list of values (or recordset if method returns records).
    ```python
    prices = properties.mapped('selling_price')
    partners = properties.mapped('partner_id') # Returns recordset
    ```
*   `filtered(func_or_field)`: Returns a subset of records.
    ```python
    # Filter by field boolean value
    sold_props = properties.filtered('is_sold')
    # Filter by lambda
    expensive_props = properties.filtered(lambda p: p.expected_price > 500000)
    ```
