---
name: Odoo Testing Core
description: Write automated tests using TransactionCase and Form emulators to ensure code quality.
---

# Odoo Testing Core

## Goal
Create automated tests that run with `odoo-bin -i my_module --test-enable`.

## 1. Test Structure
Tests are located in the `tests/` directory.
```text
my_module/
├── tests/
│   ├── __init__.py
│   └── test_estate.py
```
You MUST import `tests` in your module's `__init__.py`.

## 2. Unit Testing (`TransactionCase`)
The most common test class. It runs each test method in a separate transaction that is **rolled back** at the end. The database state is preserved between tests.

```python
from odoo.tests.common import TransactionCase, tagged
from odoo.exceptions import UserError

@tagged('post_install', '-at_install')
class TestEstateProperty(TransactionCase):

    def setUp(self):
        super(TestEstateProperty, self).setUp()
        self.property = self.env['estate.property'].create({
            'name': 'Test Property',
            'expected_price': 100000,
        })

    def test_property_creation(self):
        """Test that the property is created with correct defaults"""
        self.assertEqual(self.property.state, 'new')

    def test_selling_price_constraint(self):
        """Test that low selling price raises an error"""
        with self.assertRaises(UserError):
            self.property.selling_price = 50000 # Too low
```

## 3. The `tagged` Decorator
Controls when tests run.
*   `standard`: Runs by default.
*   `at_install`: Runs immediately after module installation (default).
*   `post_install`: Runs after all modules are installed (Recommended).
*   `nice_to_have`: Specific tag to filter tests (`odoo-bin --test-tags .nice_to_have`).

To run tests:
```bash
odoo-bin -c odoo.conf -i estate --test-enable --stop-after-init
# Or specific tags
odoo-bin -c odoo.conf --test-tags /estate
```

## 4. UI Testing within Python (`Form`)
Simulates a user opening a Form view, editing fields, and saving. This triggers `onchange` and `compute` methods automatically.

```python
from odoo.tests import Form

def test_onchange_garden(self):
    # Edit existing record
    with Form(self.property) as f:
        f.garden = True
        # verify onchange effect
        self.assertEqual(f.garden_area, 10)
        self.assertEqual(f.garden_orientation, 'north')
        
    # Create new record
    with Form(self.env['estate.property']) as f:
        f.name = "New House"
        f.expected_price = 100000
    new_prop = f.save()
```

## 5. Integration/Tour Tours (`HttpCase`)
Used for testing the Javascript UI (Tours).
(See `odoo_testing_tours` skill - *to be created if needed*).
