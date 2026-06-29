# Swagger UI Dark Theme for Django REST Framework

A modern, dark-themed Swagger UI template for Django projects using `drf-spectacular`. Features a built-in auth bar, profile panel, dark/light theme toggle, and clean overrides for every Swagger UI element.

## Features

- Dark mode (default) and light mode with persistent toggle
- Sticky auth bar with login/logout/profile directly in Swagger UI
- User profile panel showing decoded JWT token info
- Inter font + Phosphor Icons
- Fully themed: opblocks, modals, inputs, code blocks, scrollbars
- Zero config — drop in the template and it works

## Requirements

- Django 4.x+
- `drf-spectacular` (Swagger/OpenAPI schema)
- `djangorestframework-simplejwt` (if using JWT auth)

Install if you haven't:

```bash
pip install drf-spectacular djangorestframework-simplejwt
```

## Setup (3 steps)

### 1. Copy the template

Copy `templates/custom_swagger_ui.html` into your project's template directory:

```
your_project/
  templates/
    custom_swagger_ui.html   <-- put it here
```

Make sure your `settings.py` has the templates directory configured:

```python
TEMPLATES = [
    {
        ...
        "DIRS": [BASE_DIR / "templates"],
        ...
    },
]
```

### 2. Add the view subclass

Add this to your `urls.py` (or create a `views.py` in your project package):

```python
from drf_spectacular.views import SpectacularSwaggerView


class CookieAuthSwaggerView(SpectacularSwaggerView):
    template_name = "custom_swagger_ui.html"
```

Then use it in your URL patterns:

```python
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView

urlpatterns = [
    # ... your other patterns ...

    # OpenAPI schema
    path("api/schema/", SpectacularAPIView.as_view(), name="schema"),

    # Swagger UI (custom dark theme)
    path(
        "api/swagger-ui/",
        CookieAuthSwaggerView.as_view(url_name="schema"),
        name="swagger-ui",
    ),

    # ReDoc (default styling)
    path("api/redoc/", SpectacularRedocView.as_view(url_name="schema"), name="redoc"),
]
```

### 3. That's it

Run your server and visit `/api/swagger-ui/`.

## Customization

### Theme colors

All colors are CSS custom properties in `:root` (dark) and `[data-theme="light"]`. Edit the `:root` block at the top of the `<style>` section:

```css
:root {
  --accent: #6c63ff;       /* primary accent color */
  --method-get: #2dd4a8;   /* GET method badge */
  --method-post: #59acf5;  /* POST method badge */
  /* ... see full list in the template */
}
```

### Auth bar endpoints

The auth bar calls these endpoints by default:

| Action   | Endpoint          | Method |
|----------|-------------------|--------|
| Login    | `/api/auth/login/`  | POST   |
| Logout   | `/api/auth/logout/` | POST   |
| Profile  | `/api/auth/me/`     | GET    |

If your endpoints differ, update the fetch URLs in the `<script>` section. The login endpoint expects JSON `{ "username": "...", "password": "..." }`. The `/me/` endpoint should return a user object with fields like `id`, `username`, `email`, etc.

### Profile panel fields

The profile panel renders these fields from the `/me/` response. Edit the `userFields` array in the JS to match your user model:

```javascript
var userFields = [
  ['ID', user.id],
  ['Username', user.username],
  ['Email', user.email],
  // add/remove/reorder as needed
];
```

### Removing the auth bar

If you don't need the auth bar, you can remove the entire `<div id="auth-bar">` block and all the auth-related JS. The dark theme and Swagger UI overrides will still work.

### Icons

Uses [Phosphor Icons](https://phosphoricons.com) via CDN. To use any icon in your templates:

```html
<i class="ph ph-{icon-name}"></i>
```

Browse all icons at https://phosphoricons.com

## File structure

```
swagger-dark-theme/
  templates/
    custom_swagger_ui.html   # The template (copy to your project)
  README.md
  LICENSE
```

## License

MIT
