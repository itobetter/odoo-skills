---
name: Odoo Fields and ORM
description: Comprehensive guide to Odoo fields, including basic, relational, computed, and robust technical features (indexing, company_dependent, properties).
---

# Odoo Fields and ORM

## Goal
Define robust models and fields, leveraging advanced ORM features for performance, multi-company support, and dynamic customization.

## 1. Functional Requirements Checklist
Before defining a field, answer these questions to determine the correct attributes:
*   **Search/Group:** Does it need to be searchable or groupable? -> `store=True` (default for non-computed), `index=True`
*   **Performance:** Is it a large text field requiring fuzzy search? -> `index='trigram'`
*   **Multi-Company:** Does the value vary per company (e.g., Cost Price)? -> `company_dependent=True`
*   **Consistency:** Should it strictly respect company boundaries? -> `check_company=True`
*   **Pre-calculation:** Should it be computed *before* the record is created (e.g., for default values)? -> `precompute=True` (Odoo 17+)
*   **Dynamic Data:** Do users need to define custom fields per record without schema changes? -> `Properties` field.

## 2. Defining a Model
Models inherit from `models.Model`, `models.TransientModel` (temporary), or `models.AbstractModel` (mixins).

### Model Types
1.  **Model (`models.Model`)**:
    *   Regular database-persisted models.
    *   Most common type.
2.  **TransientModel (`models.TransientModel`)**:
    *   Temporary records, periodically cleaned up by a scheduled action.
    *   Used for **Wizards** (pop-ups to ask user input).
    *   No access rights required (usually).
    *   Example: `class EstateOfferWizard(models.TransientModel): ...`
3.  **AbstractModel (`models.AbstractModel`)**:
    *   No database table created.
    *   Used as a "Mixin" or Interface to be inherited by other models.
    *   Example: `mail.thread` (Chatter), `image.mixin`.

```python
from odoo import api, fields, models

class EstateProperty(models.Model):
    _name = "estate.property"
    _description = "Real Estate Property"
    _order = "id desc"
    _rec_name = "name"  # Field to use for display name

    # Robust: Prevent access by malicious users
    _allow_sudo_commands = False 
```

## 3. Basic & Advanced Fields
Defined in `odoo/odoo/orm/fields.py`.

| Field Type | Python Type | SQL Type | Notes |
| :--- | :--- | :--- | :--- |
| `Char` | `str` | `VARCHAR` | Short text. |
| `Text` | `str` | `TEXT` | Long text. |
| `Integer` | `int` | `INTEGER` | |
| `Float` | `float` | `FLOAT` | |
| `Boolean` | `bool` | `BOOLEAN` | |
| `Date` | `date` | `DATE` | `fields.Date.today()` |
| `Datetime` | `datetime` | `TIMESTAMP` | `fields.Datetime.now()` |
| `Binary` | `bytes` | `BYTEA` | Encoded in base64. |
| `Json` | `dict/list` | `JSONB` | Store structured data (Odoo 17+). |
| `Properties` | `dict` | `JSONB` | **Robust:** Pseudo-fields defined on a parent container. |

### Common Attributes
*   `string="Label"`: UI Label.
*   `required=True`: `NOT NULL` constraint.
*   `readonly=True`: UI only (unless `compute` is used).
*   `copy=False`: Don't copy this value when duplicating the record.
*   `default=...`: Static value or callable (`lambda self: ...`).
*   `groups="base.group_user"`: Restrict access to specific security groups.

### Robust Attributes
*   `index=True` / `'btree'` / `'btree_not_null'` / `'trigram'`:
    *   `'btree_not_null'`: Better performance if many values are NULL.
    *   `'trigram'`: Enables partial matching (`ilike`) acceleration (requires `pg_trgm`).
*   `company_dependent=True`: Stores values as JSONB `{'company_id': value}`.
*   `aggregator='sum'/'avg'/'max'`: Default aggregation function for Group By.

## 4. Computed Fields
Fields whose values are calculated via Python.

```python
@api.depends("living_area", "garden_area")
def _compute_total_area(self):
    for record in self:
        record.total_area = record.living_area + record.garden_area
```

| Attribute | Description | Robust Tip |
| :--- | :--- | :--- |
| `store=True` | Store in DB. Recomputed when dependencies change. | Essential for searching/grouping. |
| `compute_sudo=True` | Compute as superuser. | Default for `store=True`. |
| `precompute=True` | Compute *before* insert. | **New:** Optimization for default-like behavior on creation. |
| `recursive=True` | Allow recursive dependencies. | handle self-referencing computations. |

## 5. Relational Fields
Link models together.

### Many2one
Link to a parents.
```python
partner_id = fields.Many2one("res.partner", string="Partner", ondelete="restrict", index=True)
```
*   `ondelete`: `set null` (default), `restrict` (prevent deletion), `cascade` (delete child).
*   `domain`: Filter selectable records (e.g., `[('is_company', '=', True)]`).

### One2many
Link to children (inverse of Many2one).
```python
offer_ids = fields.One2many("estate.offer", "property_id", string="Offers")
```

### Many2many
Link to multiple records (M<->M table).
```python
tag_ids = fields.Many2many("estate.tag", string="Tags")
```

### Robust Relational Features
*   `check_company=True`: Ensures `partner_id.company_id == self.company_id`. Critical for multi-company security.
*   `context={'active_test': False}`: Include archived records in the relation.

## 6. Onchanges (`@api.onchange`)
Client-side UI logic. **Not** for data consistency (use computed fields for that).

```python
@api.onchange("partner_id")
def _onchange_partner_id(self):
    if self.partner_id:
        self.phone = self.partner_id.phone
```

## 7. The `Properties` Field (Advanced)
Allows defining fields dynamically per record, based on a "definition" from a container record.
*   **Use Case:** A "Product" has different specifications depending on the "Category".
*   Defined in `odoo/odoo/orm/fields_properties.py`.

```python
# Definition on Container (e.g., Category)
properties_definition = fields.PropertiesDefinition('Properties')

# Value on Child (e.g., Product)
properties = fields.Properties(
    string='Properties',
    definition='category_id.properties_definition',
)
```
