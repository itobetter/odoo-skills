---
name: Odoo New Application
description: setup a new Odoo module, defined the manifest, and understand the addon directory structure.
---

# Odoo New Application

## Goal
Create a new module (e.g., `estate`) to manage Real Estate Advertisements.

## Setup
1.  **Addon Directory**: Create a directory for your module in your addons path (e.g., `~/src/tutorials/estate`).
2.  **Required Files**:
    *   `__init__.py`: Can be empty initially.
    *   `__manifest__.py`: MUST contain a dictionary with at least the `name` key.

## Manifest File (`__manifest__.py`)
Describes the module to the Odoo server.

**Example:**
```python
{
    'name': 'Real Estate',
    'depends': [
        'base',  # The minimal dependency
    ],
    'application': True, # Flags it as an "App" in the Apps list
    'category': 'Real Estate',
    'summary': 'Manage real estate ads',
    'installable': True,
    'license': 'LGPL-3',
}
```

*   `name`: Title of the module.
*   `depends`: List of module technical names that must be installed before this one.
*   `application`: If `True`, appears in the "Apps" filter. Otherwise, it's a technical module.

## Registering the Module
1.  Restart the Odoo server (`odoo-bin -u ...` or just restart process).
2.  Enable **Developer Mode** in Odoo (Settings > General Settings > Activate the developer mode).
3.  Go to **Apps**.
4.  Click **Update Apps List**.
5.  Search for your module (e.g., `estate`).
6.  Click **Activate/Install**.
