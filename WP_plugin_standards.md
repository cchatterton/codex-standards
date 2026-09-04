# WordPress Plugin Build Standard

## Purpose

This standard defines how AlphaSys and Techn WordPress plugins should be scaffolded, named, organised, and handed to Codex for development.

Every plugin brief should include four inputs:

1. Codex Code Standards Sheet
2. Codex Plugin Standards Sheet
3. Branding and UX Standards Sheet
4. Plugin-specific specification

The plugin-specific specification should declare one interface branding mode:

```text
Branding Mode: Author Branded
Author Brand: AlphaSys or Techn
```

or:

```text
Branding Mode: Extension Branded
Extended Product: Gravity Forms, WooCommerce, or another named product
```

Author Branded interfaces use the declared AlphaSys or Techn identity while preserving WordPress-native behaviour. Extension Branded interfaces follow the visual and interaction conventions of the product being extended. Branding mode does not change the plugin author, prefix, text domain, licence, or repository ownership.

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
 * License: GPL v2 or later
 * License URI: https://www.gnu.org/licenses/gpl-2.0.html
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
 * License: GPL v2 or later
 * License URI: https://www.gnu.org/licenses/gpl-2.0.html
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
 * License: GPL v2 or later
 * License URI: https://www.gnu.org/licenses/gpl-2.0.html
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
├── LICENSE
├── readme.txt
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
 * License: GPL v2 or later
 * License URI: https://www.gnu.org/licenses/gpl-2.0.html
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

Use `rem` for every dimension in plugin-authored CSS, including spacing, sizing, positioning, borders, radii, typography, and media-query breakpoints.

Do not introduce `px` values. When modifying an existing CSS rule, convert any `px` dimensions in that rule to their `rem` equivalents, using `1rem = 16px` as the conversion baseline.

Third-party, vendor, and generated CSS is exempt and should not be edited solely to convert units.

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

Every distributable plugin must include a WordPress.org-format `readme.txt`, even when the plugin is currently private or distributed through GitHub.

The header must include:

```text
=== Plugin Name ===
Contributors: verified-wordpress-org-usernames
Tags: up to five relevant tags
Requires at least: 6.0
Tested up to: the latest version actually tested
Stable tag: 0.1.0
Requires PHP: 8.1
License: GPLv2 or later
License URI: https://www.gnu.org/licenses/gpl-2.0.html
```

The `Stable tag` must match the plugin header version. Do not claim a WordPress version under `Tested up to` until the plugin has actually been tested with that version. Contributor usernames must resolve to real WordPress.org profiles; confirm them rather than inventing them.

The file must include a short description of no more than 150 characters, plus Description, Installation, and Changelog sections. Add FAQ, Screenshots, Upgrade Notice, and External services sections when applicable. Disclose every external service, what data is sent, when it is sent, and links to the service's terms and privacy policy.

Validate `readme.txt` with the official WordPress readme validator before a WordPress.org submission.

A repository may also include a `readme.md` for GitHub and developer documentation. It does not replace `readme.txt`. A simple `readme.md` may use:

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

# Licensing Standard

Every WordPress plugin must be licensed under `GPL v2 or later` or another WordPress-compatible GPL licence.

The main plugin header must contain:

```text
License: GPL v2 or later
License URI: https://www.gnu.org/licenses/gpl-2.0.html
```

The distributable plugin folder must contain a `LICENSE` file that clearly declares the applicable GPL terms and links to the canonical licence text. Third-party code, fonts, images, and other bundled assets must use GPL-compatible licences, with their copyright and licence details retained and documented.

---

# Codex Build Instruction

When building a plugin from this standard, Codex must:

1. Follow the supplied Codex Code Standards Sheet
2. Follow this Codex Plugin Standards Sheet
3. Follow the Branding and UX Standards Sheet for all user-facing and administrator-facing work
4. Declare and consistently apply Author Branded or Extension Branded mode
5. Follow the plugin-specific specification
6. Use the declared author: AlphaSys or Techn
7. Use the declared plugin slug, prefix, and text domain
8. Keep the scaffold minimal
9. Avoid unnecessary files, classes, build steps, or abstractions
10. Produce working WordPress-native PHP, JS, and CSS
11. Prioritise readable code over clever code
12. Ask only if a missing decision blocks implementation
13. Treat changes to distributable plugin files as release work by default
14. Follow the repository's complete version, changelog, ZIP, commit, push, tag, and release process without requiring a separate release prompt
15. Verify the published release and expected ZIP asset before reporting completion

Repository-only documentation, tests, or development tooling changes may be committed and pushed without a plugin version release when they do not alter the distributable package. An explicit local-only, draft, review, or no-release instruction also overrides the default release requirement.

If there is a conflict between documents, priority order is:

1. Plugin-specific specification
2. Codex Plugin Standards Sheet
3. Branding and UX Standards Sheet
4. Codex Code Standards Sheet
