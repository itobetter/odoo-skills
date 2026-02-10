---
name: Odoo Actions and Buttons
description: Define buttons in views and link them to Python methods or Window Actions.
---

# Odoo Actions and Buttons

## Goal
Add interactivity to the UI using buttons that trigger Python code or other actions.

## 1. Adding Buttons to Views
Buttons are usually placed in the `<header>` of a form view or inside the `<sheet>`/`<list>`.

**Example (Header Button):**
```xml
<form>
    <header>
        <button name="action_sold" type="object" string="Sold" class="oe_highlight"/>
        <button name="action_cancel" type="object" string="Cancel"/>
    </header>
    <sheet>
        <!-- Fields -->
    </sheet>
</form>
```
*   `string`: Label of the button.
*   `class="oe_highlight"`: Highlights the button (primary color).

## 2. Button Types

### Type "object"
Triggers a Python method on the model.
*   **XML:** `type="object"`, `name="method_name"`
*   **Python:** Define a public method (no underscore prefix).

**Example:**
```python
def action_sold(self):
    for record in self:
        if record.state == 'canceled':
            raise UserError("Canceled properties cannot be sold.")
        record.state = 'sold'
    return True
```
*   **Exceptions:** Use `odoo.exceptions.UserError` to stop execution and warn the user.
*   **Return:** Always return `True` (or an action dictionary) for public methods.

### Type "action"
Triggers a specific Window Action (e.g., open a wizard, report, or another view).
*   **XML:** `type="action"`, `name="%(module_name.action_xml_id)d"`

**Example:**
```xml
<button type="action" name="%(estate.action_estate_offer)d" string="Offers"/>
```

## 3. Object Buttons inside Lists
You can add buttons to list views (or inside form sheets).
*   **Icon Buttons:** Use `icon="fa-check"` (FontAwesome) instead of `string` for compact buttons.

**Example:**
```xml
<list>
    <field name="name"/>
    <button name="action_confirm" type="object" icon="fa-check" title="Confirm"/>
    <button name="action_cancel" type="object" icon="fa-times" title="Cancel"/>
</list>
```
