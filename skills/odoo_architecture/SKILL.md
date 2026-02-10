---
name: Odoo Architecture Overview
description: Learn about Odoo's Multitier architecture, Module structure, and Editions.
---

# Odoo Architecture Overview

## Multitier Architecture
Odoo uses a three-tier architecture:
1.  **Presentation Tier**: HTML5, JavaScript, CSS (and the OWL framework).
2.  **Logic Tier**: Python (Server).
3.  **Data Tier**: PostgreSQL (RDBMS).

## Odoo Modules
Modules are the core building blocks of Odoo. They can:
*   Add new business logic.
*   Extend existing logic.
*   Be packaged as **Apps** (user-facing) or **Addons** (libraries/utils).
*   Be located in directories specified by the `--addons-path` command-line option.

### Module Composition
A module can contain:
*   **Business Objects**: Python classes (models) mapped to DB tables via ORM.
*   **Object Views**: XML/JS definitions for the UI.
*   **Data Files**: XML/CSV for initial data, configuration, security rules, reports.
*   **Web Controllers**: Handle HTTP requests.
*   **Static Assets**: Images, CSS, JS.

### Module Structure
A typical module directory structure:
```text
module_name/
├── models/
│   ├── *.py
│   └── __init__.py
├── views/
│   └── *.xml
├── data/
│   └── *.xml
├── __init__.py
└── __manifest__.py
```
*   `__init__.py`: Makes the directory a Python package and imports sub-packages/modules.
*   `__manifest__.py`: Declares the module, its dependencies, and metadata.

## Odoo Editions
*   **Community**: Open Source, core features.
*   **Enterprise**: Proprietary, extra features/apps (studio, mobile, upstream accounting, etc.) on top of Community.
