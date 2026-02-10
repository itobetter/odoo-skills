---
name: Odoo Inheritance
description: Extend existing models and views using Python and XML inheritance.
---

# Odoo Inheritance

## Goal
Modify behavior and appearance of existing Odoo applications without touching their source code.

## 1. Python Inheritance (Extension)
Add fields or methods to an existing model (e.g., `res.partner`, `res.users`).
*   **Attribute:** `_inherit = 'existing.model.name'`
*   **Note:** Do NOT define `_name` (unless you want to create a copy/prototype inheritance).

**Example:**
```python
class ResUsers(models.Model):
    _inherit = "res.users"

    property_ids = fields.One2many("estate.property", "salesperson_id", string="Properties")
```
*   **Method Overriding:** Use `super()` to maintain existing logic.
    *   `create(vals)`: Modify `vals` before calling super.
    *   `write(vals)`: Modify `vals` or trigger logic after super.
    *   `unlink()`: typically define `@api.ondelete` instead.

## 2. View Inheritance
Modify an existing XML view (Actions or Views).
*   **Model:** `ir.ui.view`
*   **Field `inherit_id`**: Reference to the parent view's XML ID.
*   **Field `arch`**: Use `xpath` expressions to locate and modify elements.

**Example:**
```xml
<record id="view_users_form_inherit_estate" model="ir.ui.view">
    <field name="name">res.users.form.inherit.estate</field>
    <field name="model">res.users</field>
    <field name="inherit_id" ref="base.view_users_form"/>
    <field name="arch" type="xml">
        <!-- Find the notebook and add a page inside it -->
        <xpath expr="//notebook" position="inside">
            <page string="Real Estate Properties">
                <field name="property_ids"/>
            </page>
        </xpath>
        
        <!-- Or find a specific field and add something after it -->
        <!-- <xpath expr="//field[@name='email']" position="after"> ... </xpath> -->
    </field>
</record>
```

### XPath Locators
*   `expr="//field[@name='description']"`: Find field by name.
*   `expr="//group"`: Find the first group.
*   `expr="//page[@string='Description']"`: Find page by label.

### XPath Positions
*   `inside`: Append to the end of the element's children.
*   `after`: Add as a sibling after the element.
*   `before`: Add as a sibling before the element.
*   `replace`: Replace the element (Use with caution!).
*   `attributes`: Modify attributes (e.g., make a field invisible).
    ```xml
    <xpath expr="//field[@name='phone']" position="attributes">
        <attribute name="required">1</attribute>
        <attribute name="invisible">1</attribute>
    </xpath>
    ```
