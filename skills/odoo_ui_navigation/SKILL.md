---
name: Odoo UI Navigation (Actions & Menus)
description: Configure Actions and Menus to expose models in the User Interface.
---

# Odoo UI Navigation (Actions & Menus)

## Goal
Create menu items and window actions to allow users to interact with the `estate.property` model.

## 1. Actions (`ir.actions.act_window`)
Actions define how the system reacts to user interactions (e.g., clicking a menu).
File location: `my_module/views/my_model_views.xml`

**Example Window Action:**
```xml
<record id="action_estate_property" model="ir.actions.act_window">
    <field name="name">Properties</field>
    <field name="res_model">estate.property</field>
    <field name="view_mode">list,form</field>
</record>
```
*   `id`: Unique XML Identifier.
*   `name`: Action name (displayed in the breadcrumbs).
*   `res_model`: The model this action is linked to.
*   `view_mode`: Comma-separated list of allowed views (e.g., `list,form`).

## 2. Menus (`ir.ui.menu`)
Menus organize the navigation.
File location: `my_module/views/my_model_menus.xml` (or same as views).

**Menu Structure:**
1.  **Root Menu** (App Switcher).
2.  **Parent Menu** (Top Bar).
3.  **Action Menu** (Dropdown/Item that triggers action).

**Example:**
```xml
<!-- Root Menu -->
<menuitem id="menu_real_estate_root" name="Real Estate">
    <!-- Parent Menu -->
    <menuitem id="menu_real_estate_first_level" name="Advertisements">
        <!-- Action Menu -->
        <menuitem id="menu_estate_property_action" action="action_estate_property"/>
    </menuitem>
</menuitem>
```

## 3. Data Files & Manifest
XML files MUST be added to the `__manifest__.py` under `data` (or `views`).
Order matters! If a menu references an action, the action must be defined first (or in a file loaded earlier).

```python
'data': [
    'security/ir.model.access.csv',
    'views/estate_property_views.xml',
    'views/estate_menus.xml',
],
```

## 4. Default Field Values
You can set default values in the Python model.
```python
active = fields.Boolean(default=True)
state = fields.Selection(..., default='new')
date_availability = fields.Date(default=lambda self: fields.Date.today() + relativedelta(months=3))
```

## 5. UI attributes
In the Python model, you can control UI behavior:
*   `readonly=True`: Field cannot be edited.
*   `copy=False`: Field value is not copied when duplicating the record.
