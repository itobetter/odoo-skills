---
name: Odoo Basic Views
description: Define List, Form, and Search views in XML.
---

# Odoo Basic Views

## Goal
Customize the List (Tree), Form, and Search views for `estate.property`.

## 1. View Declaration
Views are records in the `ir.ui.view` model, defined in XML files.

**Basic Structure:**
```xml
<record id="view_estate_property_list" model="ir.ui.view">
    <field name="name">estate.property.list</field>
    <field name="model">estate.property</field>
    <field name="arch" type="xml">
        <!-- View Architecture Goes Here -->
    </field>
</record>
```

## 2. List View (`<list>`)
Displays records in a table.
*   Root element: `<list>` (previously `<tree>`).

**Example:**
```xml
<list string="Properties">
    <field name="name"/>
    <field name="postcode"/>
    <field name="selling_price"/>
    <field name="date_availability"/>
</list>
```

## 3. Form View (`<form>`)
Displays a single record.
*   Root element: `<form>`.
*   Layout elements: `<sheet>`, `<group>`, `<notebook>`, `<page>`.

**Example:**
```xml
<form>
    <sheet>
        <group>
            <group>
                <field name="name"/>
                <field name="tag_ids" widget="many2many_tags"/>
            </group>
            <group>
                <field name="start_date"/>
            </group>
        </group>
        <notebook>
            <page string="Description">
                <field name="description"/>
            </page>
        </notebook>
    </sheet>
</form>
```

## 4. Search View (`<search>`)
Defines search, filter, and group by options.
*   Root element: `<search>`.

**Example:**
```xml
<search>
    <field name="name"/>
    <field name="postcode"/>
    
    <!-- Filter: default domain -->
    <filter string="Available" name="available" domain="[('state', 'in', ('new', 'offer_received'))]"/>
    
    <!-- Group By -->
    <filter string="Postcode" name="groupby_postcode" context="{'group_by': 'postcode'}"/>
</search>
```

### Domains
A domain is a list of criteria to select a subset of records.
Syntax: `[('field_name', 'operator', value)]`
*   `&` (AND) is default.
*   `|` (OR) prefix.
*   `!` (NOT) prefix.

**XML Safety:** Use `&lt;` for `<` and `&gt;` for `>`.

## Development Tip
Use `--dev xml` when running the server to update views on browser refresh without restarting Odoo.
`./odoo-bin -u estate --dev xml`
