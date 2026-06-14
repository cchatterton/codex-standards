# WordPress Plugin GitHub Update Standard

## Purpose

This standard defines how AlphaSys and Techn WordPress plugins should support update delivery from GitHub releases.

Use this standard when a plugin is intended to be maintained through GitHub rather than the official WordPress.org plugin directory.

The goal is:

- predictable plugin updates from the WordPress Plugins screen
- repeatable release packaging
- clear version discipline
- simple rollback/release auditing
- no manual file copying into production where a GitHub release can do the job

---

## Required Inputs

Every plugin using this update process must define:

```text
GitHub owner:
GitHub repository:
Plugin slug:
Main plugin file:
Release ZIP asset name:
Author:
Author URL:
Plugin URI:
```

Example:

```text
GitHub owner: cchatterton
GitHub repository: example-plugin
Plugin slug: techn-example-plugin
Main plugin file: techn-example-plugin/techn-example-plugin.php
Release ZIP asset name: techn-example-plugin.zip
Author: Techn
Author URL: https://techn.com.au
Plugin URI: https://github.com/cchatterton/example-plugin
```

---

## Version Discipline

Every plugin change that should be installable through WordPress must include:

1. A plugin header version bump.
2. A matching plugin version constant bump.
3. A changelog entry.
4. A GitHub release tag matching the version.
5. A release ZIP asset with the expected filename.

Example plugin header:

```php
/**
 * Plugin Name: Techn Example Plugin
 * Plugin URI: https://github.com/cchatterton/example-plugin
 * Description: Short description.
 * Version: 0.1.4
 * Requires at least: 6.0
 * Requires PHP: 8.1
 * Author: Techn
 * Author URI: https://techn.com.au
 * Text Domain: techn-example-plugin
 */
```

Example constant:

```php
define('TEP_VERSION', '0.1.4');
```

The version in the plugin header and the version constant must always match.

Use tags in this format:

```text
v0.1.4
```

The updater must strip the leading `v` before comparing versions.

---

## Changelog Standard

Every release must update `CHANGELOG.md`.

Use this format:

```markdown
# Changelog

All notable changes to Plugin Name are recorded here.

## 0.1.4 - YYYY-MM-DD

- Added X.
- Fixed Y.
- Changed Z.
```

The GitHub release notes should match the changelog entry for that version.

Do not publish a release without release notes.

---

## ZIP Package Standard

The GitHub release asset must be a ZIP file named:

```text
plugin-slug.zip
```

The ZIP must contain the plugin folder as the top-level directory:

```text
plugin-slug/
  plugin-slug.php
  readme.md
  functions/
  includes/
  admin/
  scripts/
  styles/
```

Do not upload a ZIP where the plugin files are at the ZIP root.

Do not upload a ZIP containing:

- `.git/`
- local build caches
- local environment files
- screenshots or temporary files unless required by the plugin
- `node_modules/`
- test output
- unrelated repository files

---

## Build Script Standard

Each plugin repository should include a build script:

```text
scripts/build-plugin-zip.sh
```

The script must:

- create `dist/` if it does not exist
- remove any stale copy of the plugin package
- copy only the plugin folder into `dist/`
- remove development artifacts from the copied package
- create `dist/plugin-slug.zip`
- keep the plugin folder as the ZIP top-level directory

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

PLUGIN_SLUG="techn-example-plugin"
DIST_DIR="dist"

rm -rf "$DIST_DIR/$PLUGIN_SLUG"
mkdir -p "$DIST_DIR"
cp -R "$PLUGIN_SLUG" "$DIST_DIR/$PLUGIN_SLUG"

find "$DIST_DIR/$PLUGIN_SLUG" -name ".DS_Store" -delete
rm -rf "$DIST_DIR/$PLUGIN_SLUG/node_modules"

cd "$DIST_DIR"
rm -f "$PLUGIN_SLUG.zip"
zip -qr "$PLUGIN_SLUG.zip" "$PLUGIN_SLUG"
```

Before release, verify the ZIP:

```bash
unzip -l dist/plugin-slug.zip
```

---

## WordPress Updater Requirements

Plugins distributed through GitHub must include a small GitHub release updater.

The updater must:

- check the latest GitHub release
- read the release tag
- compare the release version to the installed plugin version
- find a release asset named `plugin-slug.zip`
- inject update data into WordPress using `pre_set_site_transient_update_plugins`
- provide plugin details through the `plugins_api` filter
- cache release checks with a site transient
- fail silently and safely when GitHub is unreachable

The updater must not:

- update files directly
- call GitHub on every admin page load
- block plugin activation if GitHub is unavailable
- require a GitHub token for public release checks
- expose warnings to non-admin users
- add a custom "Check GitHub updates" link to the plugin row

---

## Required Updater Metadata

The updater should define constants or equivalent configuration for:

```php
private const OWNER = 'cchatterton';
private const REPO = 'example-plugin';
private const SLUG = 'techn-example-plugin';
private const ASSET_NAME = 'techn-example-plugin.zip';
private const RELEASE_TRANSIENT = 'tep_github_latest_release';
```

Use plugin-specific transient names.

Do not reuse transient names between plugins.

---

## GitHub Release Lookup

The updater should request:

```text
https://api.github.com/repos/{owner}/{repo}/releases/latest
```

Use WordPress HTTP APIs:

```php
wp_remote_get(
    'https://api.github.com/repos/' . self::OWNER . '/' . self::REPO . '/releases/latest',
    array(
        'timeout' => 10,
        'headers' => array(
            'Accept'     => 'application/vnd.github+json',
            'User-Agent' => 'Plugin-Name/' . PLUGIN_VERSION,
        ),
    )
);
```

Cache successful release responses for about 6 hours.

Cache failed lookups for a shorter period, such as 30 minutes.

---

## Version Comparison

The updater must:

1. Read `tag_name` from the latest GitHub release.
2. Remove a leading `v` or `V`.
3. Compare it with the installed version using `version_compare`.

Example:

```php
$version = ltrim((string) ($release['tag_name'] ?? ''), 'vV');

if (version_compare($version, PLUGIN_VERSION, '>')) {
    // Update available.
}
```

Never compare versions as plain strings.

---

## Release Asset Lookup

The updater must only offer an update if the latest release contains the expected ZIP asset.

Example:

```php
foreach ($release['assets'] as $asset) {
    if (self::ASSET_NAME === ($asset['name'] ?? '') && !empty($asset['browser_download_url'])) {
        return esc_url_raw((string) $asset['browser_download_url']);
    }
}
```

If the release exists but the ZIP asset is missing, do not offer an update.

---

## WordPress Update Injection

The updater should add an object to:

```php
$transient->response[$plugin_file]
```

Minimum fields:

```php
(object) array(
    'id'           => 'https://github.com/owner/repo',
    'slug'         => 'plugin-slug',
    'plugin'       => plugin_basename(PLUGIN_FILE),
    'new_version'  => $version,
    'url'          => $release_url,
    'package'      => $zip_download_url,
    'requires'     => '6.0',
    'requires_php' => '8.1',
)
```

Keep `requires` and `requires_php` aligned with the plugin header.

---

## Plugin Information Modal

The updater should support the WordPress "View details" modal through the `plugins_api` filter.

Minimum fields:

```php
(object) array(
    'name'          => 'Plugin Name',
    'slug'          => 'plugin-slug',
    'version'       => $version,
    'author'        => 'Techn',
    'homepage'      => 'https://github.com/owner/repo',
    'download_link' => $zip_download_url,
    'requires'      => '6.0',
    'requires_php'  => '8.1',
    'sections'      => array(
        'description' => 'Short plugin description.',
        'changelog'   => $release_body,
    ),
)
```

Use the GitHub release body as the changelog section.

---

## Plugin Row Metadata

The plugin row should include a "GitHub" metadata link.

The plugin row should not include a custom update-check action link.

WordPress should surface updates through the native plugin update UI.

If an update does not appear immediately after publishing a release, wait for WordPress update transients to refresh or use WordPress's native update mechanisms.

---

## Update Check Timing

The updater should run as part of WordPress's normal plugin update checks.

Recommended behaviour:

- use the `pre_set_site_transient_update_plugins` filter
- cache GitHub release lookups with a plugin-specific site transient
- keep successful lookups cached for about 6 hours
- keep failed lookups cached for a shorter period, such as 30 minutes
- let WordPress decide when to refresh plugin update data

Do not call GitHub on every admin page load.

---

## Settings Page Guidance

Plugin settings pages should not duplicate the WordPress plugin update UI.

Settings pages may show:

- installed version
- diagnostics
- plugin status

But update checking and updating should normally live on the Plugins page.

---

## Release Process

Use this release sequence:

1. Finish the code change.
2. Bump the plugin header version.
3. Bump the plugin version constant.
4. Add release notes to `CHANGELOG.md`.
5. Run available syntax/tests/build checks.
6. Build the plugin ZIP.
7. Verify the ZIP top-level folder.
8. Commit the source changes.
9. Push to GitHub.
10. Create a GitHub release tag matching the version.
11. Attach `plugin-slug.zip`.
12. Verify the release asset is visible.
13. In WordPress, go to the Plugins page and confirm the update is offered.
14. Confirm WordPress installs the update.

Example GitHub CLI release command:

```bash
gh release create v0.1.4 dist/plugin-slug.zip \
  --repo cchatterton/example-plugin \
  --target main \
  --title "v0.1.4" \
  --notes "## 0.1.4 - YYYY-MM-DD

- Added X.
- Fixed Y."
```

---

## Verification Checklist

Before finalising a plugin update, confirm:

- plugin activates without fatal errors
- plugin header version matches the version constant
- changelog has the new version
- ZIP contains the plugin folder at the top level
- ZIP does not include unrelated repository files
- GitHub release tag matches the plugin version
- GitHub release includes the expected ZIP asset
- WordPress detects the update from the Plugins page
- "View details" opens useful release information
- update install succeeds on a test or production site as appropriate

---

## Common Failure Modes

### WordPress Does Not See The Update

Check:

- release tag is newer than installed version
- tag uses a valid version format such as `v0.1.4`
- ZIP asset exists and has the exact expected filename
- updater owner/repo constants are correct
- GitHub latest release is not a draft
- release is not accidentally missing assets
- WordPress update transient has been cleared

### Update Installs But Plugin Disappears

Usually the ZIP root is wrong.

The ZIP must contain:

```text
plugin-slug/plugin-slug.php
```

Not:

```text
plugin-slug.php
```

### View Details Has No Useful Information

Check:

- `plugins_api` filter is implemented
- release body is being read
- plugin slug matches the updater slug

### GitHub Request Fails

The plugin should continue working.

Admin users may see a soft "could not read latest GitHub release" message only where useful.

Never make GitHub availability required for normal plugin runtime.

---

## Codex Maintenance Rule

When Codex changes a plugin that uses this standard, Codex must:

- update the version number
- update the version constant
- update release notes
- rebuild the plugin ZIP if publishing a release
- push source changes
- create the GitHub release when asked or when release publishing is in scope
- verify the release asset exists
- tell the user whether PHP lint/tests were run

If PHP is not available locally, Codex must say so in the final response.
