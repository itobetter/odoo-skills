---
name: Odoo QWeb and Kanban Views
description: Create flexible Kanban cards using QWeb templates and directives.
---

# Odoo QWeb and Kanban Views

## Goal
Visualize records as cards in a Kanban board.

## 1. Kanban View Structure
Root element is `<kanban>`. It uses `class` attributes for layout and `<templates>` for the card design.

**Example:**
```xml
<kanban default_group_by="property_type_id" records_draggable="false">
    <field name="state"/>
    <templates>
        <t t-name="card">
            <div class="oe_kanban_global_click">
                <div class="fw-bold">
                    <field name="name"/>
                </div>
                <div>
                    Expected Price: <field name="expected_price"/>
                </div>
                <!-- Conditional Display -->
                <div t-if="record.state.raw_value == 'offer_received'">
                    Best Offer: <field name="best_price"/>
                </div>
            </div>
        </t>
    </templates>
</kanban>
```

## 2. QWeb Directives
Kanban cards use QWeb (Odoo's templating engine).
*   `<t t-name="card">`: Root template for the card.
*   `t-if="condition"`: Conditionally render an element.
*   `t-foreach="list" t-as="item"`: Loop over a list.
*   `t-esc="variable"`: Output a variable (escaped).
*   `t-out="variable"`: Output a variable (trusted HTML).

## 3. Accessing Data (`record`)
In Kanban QWeb, data is accessed via the `record` object.
*   `record.field_name.value`: Formatted value (e.g., "1,000.00 $").
*   `record.field_name.raw_value`: Database value (e.g., `1000.0`).
*   **Important:** Fields used in QWeb expressions MUST be declared with `<field name="..."/>` outside the template.

## 4. Grouping & Drag/Drop
*   `default_group_by="field_name"`: Groups cards by this field (columns).
*   `records_draggable="false"`: Disables moving cards between columns.
*   `group_create="false"`: Disables creating new columns.
