---
name: Odoo Model Relations
description: Define Many2one, One2many, and Many2many fields to relate models.
---

# Odoo Model Relations

## Goal
Link the `estate.property` model to other models like Property Type, Buyer, Seller, Offers, and Tags.

## 1. Many2one (`fields.Many2one`)
Links a record to a single record in another model (Parent/Foreign Key).
*   **Database side:** Adds a column `field_id` (integer) to the table.
*   **Convention:** Field name should end with `_id` (e.g., `partner_id`).

**Example:**
```python
# In estate.property model
property_type_id = fields.Many2one("estate.property.type", string="Property Type")
buyer_id = fields.Many2one("res.partner", string="Buyer")
seller_id = fields.Many2one("res.partner", string="Seller", default=lambda self: self.env.user)
```

## 2. Many2many (`fields.Many2many`)
Bidirectional multiple relationship. A record can be related to multiple records in another model.
*   **Database side:** Creates a hidden relational table (e.g., `estate_property_tag_rel`).
*   **Convention:** Field name should end with `_ids` (e.g., `tag_ids`).

**Example:**
```python
# In estate.property model
tag_ids = fields.Many2many("estate.property.tag", string="Tags")
```
*   **Widget:** Use `widget="many2many_tags"` in the view to display as chips.

## 3. One2many (`fields.One2many`)
The inverse of a Many2one. Displays a list of records from the related model that point to the current record.
*   **Database side:** No column in the current table. It relies on the Many2one field in the co-model.
*   **Requirement:** The co-model MUST have a `Many2one` field pointing back to this model.
*   **Convention:** Field name should end with `_ids` (e.g., `offer_ids`).

**Example:**
```python
# In estate.property model
offer_ids = fields.One2many("estate.property.offer", "property_id", string="Offers")

# In estate.property.offer model (The Co-model)
property_id = fields.Many2one("estate.property", required=True)
```

## 4. The `self.env` Environment
*   `self.env`: Access to the environment.
*   `self.env.user`: Current logged-in user record.
*   `self.env.ref('xml_id')`: Get a record by its XML ID.
*   `self.env['model.name']`: Access a model registry to search/create records.

## 5. UI Tips
*   **Notebooks/Pages**: Use `<notebook>` and `<page>` in Form views to organize relational fields (like lists of offers).
*   **Inline Views**: You can define the view of a One2many field inline inside the Form view if it differs from the default list view.
