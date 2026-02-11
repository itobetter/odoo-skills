---
name: Odoo Controllers
description: Create HTTP endpoints for Websites and APIs.
---

# Odoo Controllers

## Goal
Handle HTTP requests in Odoo to build website pages or JSON APIs.

## 1. Directory Structure
Controllers are defined in the `controllers/` directory of your module.
```text
my_module/
├── controllers/
│   ├── __init__.py
│   └── main.py
```
Don't forget to import `controllers` in your module's top-level `__init__.py`.

## 2. Basic Controller Structure
Inherit from `odoo.http.Controller`.

```python
from odoo import http
from odoo.http import request

class EstateController(http.Controller):
    
    @http.route('/estate/hello', auth='public', type='http')
    def hello(self, **kwargs):
        return "Hello, World!"
```

## 3. The `@route` Decorator
*   `route`: The URL path (e.g., `/my/url`).
*   `type`:
    *   `'http'`: Standard web request. Returns HTML strings or rendered templates.
    *   `'json'`: JSON-RPC request. Returns a Python dictionary (automatically serialized to JSON).
*   `auth`:
    *   `'public'`: Accessible by anyone (even not logged in).
    *   `'user'`: Restricted to logged-in users.
    *   `'none'`: Very low-level (no database cursor/user). Rarely used.
*   `methods`: List of HTTP methods (e.g., `['POST', 'GET']`). Default is all.
*   `cors`: CORS headers (e.g., `'*'`).

## 4. The `request` Object
*   `request.env`: Access the ORM environment (if `auth='user'` or `public`).
*   `request.params`: Dictionary of query parameters (GET) and body parameters (POST).
*   `request.httprequest`: The underlying Werkzeug request object.

## 5. Returning Responses

### Rendering Templates (Website)
Use `request.render(template_xml_id, values)`:
```python
@http.route('/estate/properties', auth='public', website=True)
def list_properties(self, **kw):
    properties = request.env['estate.property'].search([])
    return request.render('estate.property_list_template', {
        'properties': properties
    })
```
*   `website=True`: Adds website context (menus, footer, user session).

### Returning JSON (API)
```python
@http.route('/estate/api/properties', auth='public', type='json', methods=['POST'])
def api_properties(self):
    properties = request.env['estate.property'].search([])
    return [
        {'name': p.name, 'price': p.expected_price} 
        for p in properties
    ]
```

## 6. Override Existing Routes
Inherit the controller and redefine the method with the same name.
```python
from odoo.addons.website_sale.controllers.main import WebsiteSale

class WebsiteSaleInherit(WebsiteSale):
    @http.route()
    def shop(self, page=0, category=None, search='', ppg=False, **post):
        # Do something before
        response = super().shop(page, category, search, ppg, **post)
        # Do something after
        return response
```
