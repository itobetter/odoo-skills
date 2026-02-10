---
name: Odoo Best Practices and Coding Guidelines
description: Follow official Odoo coding standards, naming conventions, and testing procedures.
---

# Odoo Best Practices and Coding Guidelines

## Goal
Write clean, maintainable, and "Odoo-way" code that passes standard linters and reviews.

## 1. Naming Conventions
*   **Models:** `module.name` (singular). Example: `estate.property`.
*   **Python Classes:** PascalCase. Example: `EstateProperty`.
*   **Fields:** snake_case.
    *   `Many2one`: `partner_id`, `user_id` (suffix `_id`).
    *   `One2many`/`Many2many`: `line_ids`, `tag_ids` (suffix `_ids`).
*   **Methods:**
    *   `action_...`: Public methods triggered by buttons (e.g., `action_sold`).
    *   `_compute_...`: Compute methods (e.g., `_compute_total`).
    *   `_onchange_...`: Onchange methods.
*   **files:** `model_name.py` (e.g., `estate_property.py`).

## 2. Model Structure (Python)
Order attributes and methods logically:
1.  Private attributes (`_name`, `_description`, `_inherit`, `_order`).
2.  Default methods (`default_get`).
3.  Fields declaration.
4.  Compute, inverse, and search methods.
5.  Selection methods (if any).
6.  Constrains methods (`@api.constrains`).
7.  Onchange methods (`@api.onchange`).
8.  CRUD methods (`create`, `write`, `unlink`).
9.  Action methods (`action_...`).
10. Business methods.

## 3. XML Guidelines
*   **XML IDs:** `model_name_view_type` (e.g., `estate_property_view_form`, `estate_property_action`).
*   **Ref:** Always use the full external ID `module.xml_id` when referring to records/views, even within the same module (best practice for clarity).
*   **Groups:** Use `<group>` to organize form views.

## 4. Security
*   **CSV:** Always define `ir.model.access.csv` for new models.
*   **Groups:** Hide sensitive menus/actions using `groups="base.group_user"` or custom groups.
*   **Company Check:** For multi-company environments, ensure records are filtered/validated against `company_id`.

## 5. Testing & Runbot
*   **Runbot:** Odoo's CI server. Ensure your module installs without errors on a fresh database.
*   **Linter:** Use `pylint-odoo` to catch common mistakes like missing descriptions, wrong attribute order, or security holes.
