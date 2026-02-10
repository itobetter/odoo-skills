---
name: Odoo UI Improvements (Sprinkles)
description: Enhance the UI with inline views, widgets, ordering, and stat buttons.
---

# Odoo UI Improvements (Sprinkles)

## Goal
Polish the user interface with advanced view features, smart buttons, and better data organization.

## 1. Inline Views
Define a specific view for a One2many field directly inside the parent Form view, instead of using the default registered list view.
*   **Use Case:** Showing a simplified list of items within a form.

**Example:**
```xml
<field name="offer_ids">
    <list editable="bottom" decoration-danger="status=='refused'" decoration-success="status=='accepted'">
        <field name="price"/>
        <field name="partner_id"/>
        <field name="status" invisible="1"/>
    </list>
</field>
```

## 2. Widgets
Change how a field is rendered in the UI.
*   **Status Bar:** `widget="statusbar"` (Field type: Selection).
*   **Tags:** `widget="many2many_tags"` (Field type: Many2many).
*   **Handle:** `widget="handle"` (Field type: Integer, for manual ordering).
*   **Monetary:** `widget="monetary"` (Requires a currency field).

**Example:**
```xml
<field name="state" widget="statusbar" statusbar_visible="new,offer_received,offer_accepted,sold"/>
```

## 3. Ordering Records
Deterministic ordering of records is crucial for usability.

### Model Ordering (`_order`)
Sets the default sort order for searching and listing.
```python
_order = "id desc"
# or
_order = "sequence, id desc"
```

### Manual Ordering (`handle`)
Allows users to drag and drop rows in a list.
1. Add an integer field `sequence` (default=10).
2. Add `_order = "sequence, id"` to the model.
3. Add `<field name="sequence" widget="handle"/>` as the first column in the list view.

## 4. Stat Buttons (Smart Buttons)
Quick access links in the top-right of a form view.
*   **Class:** `oe_stat_button`
*   **Icon:** `fa-star`, `fa-book`, etc.

**Example:**
```xml
<div class="oe_button_box" name="button_box">
    <button name="action_view_offers" type="object" class="oe_stat_button" icon="fa-money">
        <field name="offer_count" widget="statinfo" string="Offers"/>
    </button>
</div>
```
*   **Pattern:** Usually implemented with a computed field (`offer_count`) and an action (`action_view_offers`) that opens the related records.
