# WordPress Plugin Build Standard

## Purpose

This standard defines how AlphaSys and Techn WordPress plugins should be scaffolded, named, organised, and handed to Codex for development.

Every plugin brief should include three inputs:

1. Codex Code Standards Sheet
2. Codex Plugin Standards Sheet
3. Plugin-specific specification

The plugin-specific specification must clearly declare whether the plugin is:

```text
Author: AlphaSys
```

or

```text
Author: Techn
```

Do not use Fusion as the default author or namespace unless specifically requested.

---

# Author Declaration

Each plugin must declare its author in the plugin header.

Example:

```php
/**
 * Plugin Name: AlphaSys Example Plugin
 * Description: Short description of what the plugin does.
 * Version: 0.1.0
 * Author: AlphaSys
 * Text Domain: alphasys-example-plugin
 */
```

or:

```php
/**
 * Plugin Name: Techn Example Plugin
 * Description: Short description of what the plugin does.
 * Version: 0.1.0
 * Author: Techn
 * Text Domain: techn-example-plugin
 */
```

---

# Techn Plugin Display Names

Techn plugins should use the `TN` brand prefix in the visible WordPress plugin name unless the plugin-specific brief explicitly requires a different display name.

Use:

```text
Plugin Name: TN Journey Graph
Plugin Name: TN Sticky Posts
```

Do not use a generic feature name as the visible plugin name when the plugin is a Techn-owned plugin.

For Techn plugins:

```text
Author: Techn
Author URI: https://techn.com.au
```

The repository slug and text domain should still use the declared plugin slug.

Example:

```php
/**
 * Plugin Name: TN Example Plugin
 * Description: Short description of what the plugin does.
 * Version: 0.1.0
 * Author: Techn
 * Author URI: https://techn.com.au
 * Text Domain: tn-example-plugin
 */
```

---

# Naming Convention

Every plugin must use a short, unique prefix.

The prefix should usually be based on the plugin name, not the author.

Example:

```text
Plugin Name: AlphaSys Managed Services Portal
Slug: alphasys-managed-services-portal
Prefix: amsp
Text Domain: alphasys-managed-services-portal
Author: AlphaSys
```

Example:

```text
Plugin Name: Techn Content Sync
Slug: techn-content-sync
Prefix: tcs
Text Domain: techn-content-sync
Author: Techn
```

Use the prefix consistently for:

```php
amsp_function_name()
AMSP_CONSTANT_NAME
amsp_option_name
amsp_meta_key
amsp_script_handle
amsp_style_handle
amsp_rest_route
```

Do not use generic function names.

Do not use `fusion_` unless the plugin specification explicitly requires it.

---

# Folder Scheme

Use this as the default scaffold:

```text
plugin-slug/
├── plugin-slug.php
├── readme.md
├── functions/
│   ├── setup.php
│   ├── admin.php
│   ├── assets.php
│   ├── rest.php
│   └── helpers.php
├── scripts/
│   └── plugin-slug.js
├── styles/
│   └── plugin-slug.css
└── templates/
    └── .gitkeep
```

Only add more files when the plugin genuinely needs them.

For MVP or POC plugins, keep the scaffold as small as possible.

---

# Core File Scaffold

The main plugin file should only:

1. Declare the plugin header
2. Define constants
3. Load required files
4. Register activation/deactivation hooks if needed

Example:

```php
<?php
/**
 * Plugin Name: AlphaSys Example Plugin
 * Description: Short description of the plugin.
 * Version: 0.1.0
 * Author: AlphaSys
 * Text Domain: alphasys-example-plugin
 */

if (!defined('ABSPATH')) {
    exit;
}

define('AEP_VERSION', '0.1.0');
define('AEP_PLUGIN_FILE', __FILE__);
define('AEP_PLUGIN_DIR', plugin_dir_path(__FILE__));
define('AEP_PLUGIN_URL', plugin_dir_url(__FILE__));

require_once AEP_PLUGIN_DIR . 'functions/setup.php';
require_once AEP_PLUGIN_DIR . 'functions/assets.php';
require_once AEP_PLUGIN_DIR . 'functions/admin.php';
require_once AEP_PLUGIN_DIR . 'functions/rest.php';
require_once AEP_PLUGIN_DIR . 'functions/helpers.php';
```

---

# Function File Responsibilities

## `functions/setup.php`

Use for:

* post types
* taxonomies
* activation setup
* default options
* capability setup

## `functions/assets.php`

Use for:

* enqueueing CSS
* enqueueing JavaScript
* localising scripts
* frontend/admin asset loading

## `functions/admin.php`

Use for:

* admin menus
* admin pages
* settings pages
* meta boxes
* admin-only actions

## `functions/rest.php`

Use for:

* REST route registration
* REST callbacks
* REST permissions
* JSON responses

## `functions/helpers.php`

Use for:

* shared utility functions
* sanitisation helpers
* formatting helpers
* repeated logic used across files

---

# Hook Naming

Hook callbacks must be named using the plugin prefix.

Example:

```php
add_action('init', 'amsp_register_post_types');

function amsp_register_post_types() {
    // ...
}
```

Avoid anonymous functions unless the callback is tiny and does not need to be reused or removed.

---

# REST Route Standard

Use the plugin slug or prefix as the namespace.

Example:

```php
register_rest_route('amsp/v1', '/reports', [
    'methods'             => 'GET',
    'callback'            => 'amsp_get_reports',
    'permission_callback' => 'amsp_rest_permissions',
]);
```

Every REST route must have a permission callback.

Do not use:

```php
'permission_callback' => '__return_true'
```

unless the plugin specification explicitly confirms the route is public.

---

# Admin Pages

Admin pages should use standard WordPress admin UI patterns.

Use:

```php
add_menu_page()
add_submenu_page()
```

Admin page callbacks should stay simple. If the page becomes large, move markup into a template file.

---

# Assets

Scripts and styles must be registered/enqueued through WordPress.

Do not hardcode asset URLs.

Example:

```php
wp_enqueue_script(
    'amsp-admin',
    AMSP_PLUGIN_URL . 'scripts/alphasys-managed-services-portal.js',
    ['jquery'],
    AMSP_VERSION,
    true
);
```

---

# Security Standards

All plugin code must follow WordPress security basics:

* escape output with `esc_html`, `esc_attr`, `esc_url`, or `wp_kses_post`
* sanitise input with the appropriate sanitiser
* verify nonces for form submissions
* check capabilities before admin actions
* use prepared SQL statements
* avoid direct file access
* avoid exposing sensitive data through REST

---

# Data Storage

Prefer WordPress-native storage first:

* options for plugin settings
* post meta for object-level metadata
* custom post types for manageable records
* transients for temporary cache
* custom tables only when the data volume or query pattern clearly requires it

Do not create custom tables by default.

---

# CSS Standard

Use one CSS file unless the plugin has a clear reason to split files.

Class names should use the plugin prefix.

Example:

```css
.amsp-admin-wrap {
}

.amsp-report-table {
}

.amsp-status-pill {
}
```

Avoid global styling.

Do not style generic elements without a plugin wrapper.

---

# JavaScript Standard

Use one JavaScript file unless the plugin has a clear reason to split files.

Use the plugin prefix for global objects.

Example:

```js
window.AMSP = window.AMSP || {};
```

Avoid unnecessary frameworks.

Use vanilla JS unless the plugin specifically requires jQuery or another library.

---

# Readme Standard

Each plugin must include a simple `readme.md` with:

```text
# Plugin Name

Author: AlphaSys or Techn  
Version: 0.1.0  
Status: POC / MVP / Production  

## Purpose

## Key Features

## Folder Structure

## Important Notes

## Future Considerations
```

---

# Codex Build Instruction

When building a plugin from this standard, Codex must:

1. Follow the supplied Codex Code Standards Sheet
2. Follow this Codex Plugin Standards Sheet
3. Follow the plugin-specific specification
4. Use the declared author: AlphaSys or Techn
5. Use the declared plugin slug, prefix, and text domain
6. Keep the scaffold minimal
7. Avoid unnecessary files, classes, build steps, or abstractions
8. Produce working WordPress-native PHP, JS, and CSS
9. Prioritise readable code over clever code
10. Ask only if a missing decision blocks implementation

If there is a conflict between documents, priority order is:

1. Plugin-specific specification
2. Codex Plugin Standards Sheet
3. Codex Code Standards Sheet
