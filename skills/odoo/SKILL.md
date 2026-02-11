---
name: Odoo Router
description: A router skill that identifies and recommends the most appropriate Odoo skill for a given user request.
---

# Odoo Router Skill

This skill acts as a router to help the Agent identify the best specialized Odoo skill to apply for a user's request.

## How to Use

1.  **Analyze the User Request**: Identify the core intent of the user (e.g., creating a field, building a view, writing a test).
2.  **Consult the Routing Table**: Look up the intent in the table below to find the recommended skill.
3.  **Apply the Target Skill**: Open and read the `SKILL.md` of the target skill to proceed with the specific implementation.

## Routing Table

| User Intent / Keyword | Target Skill | Path |
| :--- | :--- | :--- |
| **New Module/App** | | |
| Create new module, scaffold, manifest, structure | `odoo_new_application` | `/home/ubuntu/.agent/skills/odoo_new_application` |
| **Data Models & Fields** | | |
| Add simple fields (char, int, bool, date) | `odoo_fields` | `/home/ubuntu/.agent/skills/odoo_fields` |
| Add relational fields (Many2one, One2many, Many2many) | `odoo_model_relations` | `/home/ubuntu/.agent/skills/odoo_model_relations` |
| Computed fields, default values | `odoo_fields` | `/home/ubuntu/.agent/skills/odoo_fields` |
| SQL Constraints, Python Constraints | `odoo_constraints` | `/home/ubuntu/.agent/skills/odoo_constraints` |
| Inheritance, `_inherit`, extending models | `odoo_inheritance` | `/home/ubuntu/.agent/skills/odoo_inheritance` |
| **Views & UI** | | |
| List (tree), Form, Search views | `odoo_views_basic` | `/home/ubuntu/.agent/skills/odoo_views_basic` |
| Kanban views, QWeb templates | `odoo_qweb_kanban` | `/home/ubuntu/.agent/skills/odoo_qweb_kanban` |
| Buttons, Server Actions | `odoo_actions_buttons` | `/home/ubuntu/.agent/skills/odoo_actions_buttons` |
| Menus, Window Actions | `odoo_ui_navigation` | `/home/ubuntu/.agent/skills/odoo_ui_navigation` |
| Smart buttons, stat buttons, widgets | `odoo_ui_improvements` | `/home/ubuntu/.agent/skills/odoo_ui_improvements` |
| Inherit/Modify existing views | `odoo_inheritance` | `/home/ubuntu/.agent/skills/odoo_inheritance` |
| **Logic & API** | | |
| ORM methods (create, write, search, unlink) | `odoo_orm_api` | `/home/ubuntu/.agent/skills/odoo_orm_api` |
| HTTP Controllers, Routes, JSON/Validation | `odoo_controllers` | `/home/ubuntu/.agent/skills/odoo_controllers` |
| Cross-module interaction | `odoo_module_interaction` | `/home/ubuntu/.agent/skills/odoo_module_interaction` |
| **Security** | | |
| ACLs (ir.model.access.csv), Record Rules | `odoo_security_introduction` | `/home/ubuntu/.agent/skills/odoo_security_introduction` |
| **Quality Assurance** | | |
| Unit tests, TransactionCase, Form tests | `odoo_testing_core` | `/home/ubuntu/.agent/skills/odoo_testing_core` |
| Coding standards, linting, best practices | `odoo_best_practices` | `/home/ubuntu/.agent/skills/odoo_best_practices` |
| **General Architecture** | | |
| Architecture overview, layers | `odoo_architecture` | `/home/ubuntu/.agent/skills/odoo_architecture` |

## Example Scenarios

- **Request**: "I need to add a 'deadline' date field to the task model."
    - **Match**: Add simple fields -> `odoo_fields`
- **Request**: "Create a Kanban view for my project tickets."
    - **Match**: Kanban views -> `odoo_qweb_kanban`
- **Request**: "How do I check if a user has permission to view this record?"
    - **Match**: Record Rules/Security -> `odoo_security_introduction` (and maybe `odoo_orm_api` for `check_access_rights`)
