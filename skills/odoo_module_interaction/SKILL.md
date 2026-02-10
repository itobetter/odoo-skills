---
name: Odoo Module Interaction
description: Interact with other modules, create link modules, and trigger cross-module logic.
---

# Odoo Module Interaction

## Goal
Extend functionality by leveraging other installed modules (e.g., creating an Invoice from the Real Estate module).

## 1. Link Modules
A "Link Module" is a module designed to glue two other independent modules together.
*   **Dependencies:** In `__manifest__.py`, depend on both modules.
    ```python
    'depends': ['estate', 'account'],
    ```
*   **Purpose:** Keeps the original modules decoupled. `estate` doesn't need to know about `account`, and vice-versa.

## 2. Cross-Module Model Inheritance
To extend logic from another module, inherit the model and override methods.

**Example: Trigger Invoice Creation on Property Sale**
```python
class EstateProperty(models.Model):
    _inherit = "estate.property"

    def action_sold(self):
        # 1. Call super to execute original logic (set state to sold)
        res = super().action_sold()
        
        # 2. Add new logic (Create Invoice)
        for prop in self:
            self.env["account.move"].create({
                "partner_id": prop.buyer_id.id,
                "move_type": "out_invoice",
                "line_ids": [
                    Command.create({
                        "name": prop.name,
                        "quantity": 1,
                        "price_unit": prop.selling_price * 0.06,
                    }),
                    Command.create({
                        "name": "Administrative Fees",
                        "quantity": 1,
                        "price_unit": 100.00,
                    }),
                ],
            })
        return res
```

## 3. Creating Records Programmatically
Use `self.env['model.name'].create(values_dict)`.
*   **Values Dict:** Keys are field names, values are the data.
*   **Relational Fields:**
    *   `Many2one`: Pass the integer ID (e.g., `'partner_id': 5`).
    *   `One2many`/`Many2many`: Use `Command` tuples (e.g., `[Command.create({...})]`).

## 4. Debugging Tips
*   Always check if the module is installed.
*   Use `print()` or a debugger to verify method overrides are triggering.
*   Ensure users have access rights to the models you are interacting with.
