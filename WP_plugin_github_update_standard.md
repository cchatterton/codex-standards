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
Update URI:
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
Update URI: https://github.com/cchatterton/example-plugin
```

---

## Repository Visibility

Plugins using the standard unauthenticated GitHub release updater must be hosted in a public GitHub repository, or otherwise expose a publicly downloadable release ZIP asset.

Do not create plugin repositories as private unless the plugin specification explicitly requires private distribution and the updater is extended to support authenticated GitHub API and asset requests.

For the default Techn/AlphaSys GitHub updater pattern, use public repositories so WordPress can discover `/releases/latest` and download the expected ZIP asset without a token.

---

## Plugin Header Update Source

Every GitHub-distributed plugin must include an `Update URI` header and must omit the `Plugin URI` header.

WordPress automatically renders `Plugin URI` as a **Visit plugin site** link on the Plugins screen. Do not declare `Plugin URI`; the GitHub and native update links supplied by the updater are the approved discovery and update paths.

When maintaining an existing plugin that still declares `Plugin URI`, remove that header in the next plugin release. Treat this as required release maintenance even when the feature change is otherwise unrelated. Confirm the resulting Plugins screen no longer shows **Visit plugin site** for that plugin.

Removing **Visit plugin site** must not remove the WordPress-side update affordance. Treat these as separate, simultaneous requirements:

- the plugin header must omit `Plugin URI`, so **Visit plugin site** is absent
- the active plugin must add its own nonce-protected **Check for updates** row link for users who can update plugins

Use `Update URI` as the stable source identifier for the GitHub updater and to prevent accidental WordPress.org slug matching:

```text
Update URI: https://github.com/{owner}/{repo}
```

The required update workflow must happen inside WordPress. The user must either be prompted by a native WordPress update notice or be able to trigger a WordPress-side update check that returns a native "update now" action without needing to visit GitHub.

The self-contained updater must inject native WordPress update notices whenever the plugin is active in the current admin context.

---

## Version Discipline

Every plugin change that should be installable through WordPress must include:

1. A plugin header version bump.
2. A matching plugin version constant bump.
3. A changelog entry.
4. A GitHub release tag matching the version.
5. A root-level release ZIP with the expected filename.
6. A GitHub release ZIP asset with the expected filename.

Example plugin header:

```php
/**
 * Plugin Name: Techn Example Plugin
 * Description: Short description.
 * Version: 0.1.4
 * Requires at least: 6.0
 * Requires PHP: 8.1
 * Update URI: https://github.com/owner/repo
 * Author: Techn
 * Author URI: https://techn.com.au
 * License: GPL v2 or later
 * License URI: https://www.gnu.org/licenses/gpl-2.0.html
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

The repository root must also contain a committed copy of the same release ZIP:

```text
plugin-slug.zip
```

The root ZIP is for direct manual upload through the WordPress plugin uploader. The GitHub release asset remains the source used by the WordPress plugin updater.

The ZIP must contain the plugin folder as the top-level directory:

```text
plugin-slug/
  plugin-slug.php
  LICENSE
  readme.txt
  readme.md
  functions/
  includes/
  admin/
  scripts/
  styles/
```

Do not upload a ZIP where the plugin files are at the ZIP root.

Do not commit or upload a ZIP that contains another copy of `plugin-slug.zip`.

Do not upload a ZIP containing:

- `.git/`
- local build caches
- local environment files
- screenshots or temporary files unless required by the plugin
- `node_modules/`
- test output
- unrelated repository files

The repository root ZIP must be generated by the build script. Do not assemble it manually.

---

## Build Script Standard

Each plugin repository should include a build script:

```text
scripts/build-plugin-zip.sh
```

The script must:

- create `dist/` if it does not exist
- remove any stale copy of the plugin package
- remove any stale root `plugin-slug.zip`
- copy only the plugin folder into `dist/`
- remove development artifacts from the copied package
- create `dist/plugin-slug.zip`
- copy `dist/plugin-slug.zip` to `plugin-slug.zip` in the repository root
- keep the plugin folder as the ZIP top-level directory
- avoid including the root ZIP inside the generated ZIP

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

PLUGIN_SLUG="techn-example-plugin"
DIST_DIR="dist"

rm -rf "$DIST_DIR/$PLUGIN_SLUG"
rm -f "$PLUGIN_SLUG.zip"
mkdir -p "$DIST_DIR"
cp -R "$PLUGIN_SLUG" "$DIST_DIR/$PLUGIN_SLUG"

find "$DIST_DIR/$PLUGIN_SLUG" -name ".DS_Store" -delete
rm -rf "$DIST_DIR/$PLUGIN_SLUG/node_modules"

cd "$DIST_DIR"
rm -f "$PLUGIN_SLUG.zip"
zip -qr "$PLUGIN_SLUG.zip" "$PLUGIN_SLUG"
cp "$PLUGIN_SLUG.zip" "../$PLUGIN_SLUG.zip"
```

Before release, verify the ZIP:

```bash
unzip -l dist/plugin-slug.zip
unzip -l plugin-slug.zip
```

---

## WordPress Updater Requirements

Plugins distributed through GitHub must include a small GitHub release updater.

The updater must support a fully WordPress-native update flow. Administrators must be able to discover, check for, and install updates from WordPress admin without visiting the GitHub repository or manually downloading the release ZIP.

The updater must:

- check the latest GitHub release
- read the release tag
- compare the release version to the installed plugin version
- find a release asset named `plugin-slug.zip`
- inject update data into WordPress using `pre_set_site_transient_update_plugins`
- also inject update data through `site_transient_update_plugins` so the Plugins screen can recover when WordPress is reading an existing update transient
- expose a WordPress-admin "Check for updates" affordance when the plugin is loaded in the current admin context
- remove stale update data for the plugin when the latest release is not newer than the installed version
- provide plugin details through the `plugins_api` filter
- cache release checks with a site transient
- avoid long-lived "no update" release caches that hide a release published shortly after a check
- never store failed GitHub lookups in the same transient used as release data
- store failed lookup diagnostics in a separate short-lived diagnostic transient when diagnostics are useful
- use a separate short-lived failure backoff so repeated admin page loads do not retry a failed remote lookup
- clear the plugin-specific GitHub release cache and any diagnostic transient after successful plugin upgrader runs
- fail silently and safely when GitHub is unreachable

The updater must not:

- update files directly
- call GitHub on every admin page load
- block plugin activation if GitHub is unavailable
- require a GitHub token for public release checks
- expose warnings to non-admin users

---

## Required Updater Metadata

The updater should define constants or equivalent configuration for:

```php
private const OWNER = 'cchatterton';
private const REPO = 'example-plugin';
private const SLUG = 'techn-example-plugin';
private const ASSET_NAME = 'techn-example-plugin.zip';
private const RELEASE_TRANSIENT = 'tep_github_latest_release';
private const ERROR_TRANSIENT = 'tep_github_latest_release_error';
```

Use plugin-specific transient names.

Do not reuse transient names between plugins.

---

## GitHub Release Lookup

Every public GitHub-distributed plugin must use a small repository-controlled manifest as its first release lookup:

```text
https://raw.githubusercontent.com/{owner}/{repo}/main/update.json
```

The root-level manifest should contain only stable release metadata:

```json
{
  "version": "0.1.4",
  "body": "Short release summary."
}
```

Construct the release details URL and expected ZIP URL from the configured repository, validated version, and configured asset name. Do not accept an arbitrary package URL from the manifest.

This path avoids unauthenticated GitHub API quotas shared by managed hosting providers. Update the manifest in the same commit as the version bump and root release ZIP.

The manifest lookup is mandatory, not an optional optimisation. A normal successful update check must not call the GitHub API when the manifest is available and valid.

Use this fallback order:

1. Repository-controlled `update.json` manifest.
2. GitHub's public latest-release redirect.
3. GitHub's latest-release API only as a last resort.

This order prevents a fleet of WordPress sites from consuming the low unauthenticated API quota associated with a shared hosting IP address.

The updater may use the GitHub latest-release API only when both the manifest and public latest-release redirect are unavailable or invalid:

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

Cache successful release responses carefully:

- if the latest GitHub version is newer than the installed plugin version, cache the response for about 6 hours
- if the latest GitHub version is equal to or older than the installed plugin version, cache the response for a short period only, such as 1-15 minutes
- if the release response cannot be parsed into a valid version, do not store it in the release transient

Do not cache an equal-version release response for several hours. This can hide a release that is published shortly after the plugin checks GitHub.

Do not cache failed lookups in the release transient. A failed lookup is not release data and must not be allowed to block future update notices.

When the manifest is unavailable or invalid, the updater should next use GitHub's public latest-release redirect:

```text
https://github.com/{owner}/{repo}/releases/latest
```

The fallback must:

- read the redirected release tag from `/releases/tag/{tag}`
- strip the leading `v` before version comparison
- construct the expected release asset URL using the configured asset filename
- store the fallback release data in the same successful release transient shape
- clear any diagnostic transient after a successful fallback lookup

Do not probe a GitHub release asset with `wp_remote_head()` before offering the update. GitHub's signed asset delivery path can reject `HEAD` from managed hosting networks even when WordPress's normal `GET` download succeeds. Asset existence is guaranteed by the release checklist and is verified by the actual WordPress upgrader request.

The fallback must not offer an update if the redirected tag cannot be parsed into a valid version.

### Rate-limit handling

HTTP `429 Too Many Requests` and GitHub API rate-limit responses must be treated as expected temporary lookup failures.

The updater must:

- stop the current lookup without retrying the same rate-limited endpoint
- avoid calling the GitHub API again from ordinary admin page loads during a short backoff period, such as 10 minutes
- keep backoff or diagnostic state separate from the successful release transient
- allow an authorised, nonce-protected manual update check to clear the backoff and try the manifest again
- continue normal plugin operation while update discovery is unavailable
- show only a generic failure notice after a manual check

The updater must not:

- expose raw GitHub response messages such as `Too Many Requests` in WordPress notices
- retry a rate-limited request repeatedly during the same update check
- call the GitHub API merely to enrich release details when a valid manifest already supplied the current version and release summary

If diagnostics are useful, store failed lookup details in a separate plugin-specific diagnostic transient for a short period, such as 10 minutes. The diagnostic transient may include:

- error type, such as `wp_error`, `http_error`, or `json_error`
- HTTP response code and message when available
- WordPress error message when available
- a short body excerpt when useful
- checked timestamp

The updater must not treat the diagnostic transient as update state.

Bypass the plugin-specific GitHub release cache when WordPress is performing a forced update check, such as the native "Check again" action on the Updates screen.

Forced update checks should delete or ignore the plugin-specific release transient before calling GitHub so newly published releases can be detected immediately.

Forced update detection should recognise both GET and POST request shapes used by WordPress update screens. At minimum, check for:

- `force-check`
- `action=update-selected`
- `action=upgrade-plugin`
- `action=do-plugin-upgrade`

Only treat these as forced checks for users who can `update_plugins`.

Do not bypass caching on every admin page load.

The plugin-specific release cache should be cleared after a successful plugin update using `upgrader_process_complete`.

Clearing release cache should also clear any plugin-specific diagnostic transient.

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

Before returning the update transient, the updater must remove stale update entries for its own plugin when the latest GitHub release is equal to or older than the installed version.

This prevents WordPress from continuing to display an old update notice after the plugin has already been updated to the advertised version.

The updater must hook both:

```php
add_filter('pre_set_site_transient_update_plugins', array($updater, 'add_update_data'));
add_filter('site_transient_update_plugins', array($updater, 'add_update_data'));
```

`pre_set_site_transient_update_plugins` updates the transient when WordPress refreshes plugin update data.

`site_transient_update_plugins` allows the plugin to add or repair its own update entry when WordPress is reading an existing transient on the Plugins screen.

The `site_transient_update_plugins` path must still use the plugin-specific GitHub release cache and must not call GitHub on every admin page load.

Before writing to `response` or `no_update`, ensure those properties are arrays:

```php
$transient->response = isset($transient->response) && is_array($transient->response) ? $transient->response : array();
$transient->no_update = isset($transient->no_update) && is_array($transient->no_update) ? $transient->no_update : array();
```

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

When no update is available, clear the plugin's stale response entry.

Prefer not to populate a plugin-specific `no_update` entry for GitHub-distributed plugins. WordPress and host caches can keep stale `no_update` data around longer than expected, which can hide newly published GitHub releases during hotfix workflows.

```php
unset($transient->response[$plugin_file]);
unset($transient->no_update[$plugin_file]);
```

If a plugin has a specific need to populate `no_update`, it must only do so after a successful, valid GitHub release lookup confirms there is no newer version. It must never write `no_update` after a failed GitHub lookup.

Example optional `no_update` object:

```php
unset($transient->response[$plugin_file]);

$transient->no_update[$plugin_file] = (object) array(
    'id'           => 'https://github.com/owner/repo',
    'slug'         => 'plugin-slug',
    'plugin'       => $plugin_file,
    'new_version'  => PLUGIN_VERSION,
    'url'          => 'https://github.com/owner/repo',
    'package'      => '',
    'requires'     => '6.0',
    'requires_php' => '8.1',
);
```

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

The plugin row must include a "GitHub" metadata link when the plugin is loaded in the current admin context.

The plugin row must also include a "Check for updates" metadata link when the plugin is loaded in the current admin context.

The expected Plugins-screen result is a **GitHub** metadata link and a **Check for updates** metadata link, with no **Visit plugin site** link. Do not use `Plugin URI` to produce or replace either required metadata link.

The "Check for updates" link should point to a nonce-protected plugin-page action handled by the plugin, for example:

```php
$plugins_url = is_multisite() ? network_admin_url('plugins.php') : admin_url('plugins.php');
$check_url = wp_nonce_url(add_query_arg('example_check_updates', '1', $plugins_url), 'example_check_updates');
```

The handler must:

1. verify the nonce
2. check `update_plugins`
3. clear the plugin-specific GitHub release and diagnostic transients
4. clear WordPress's `update_plugins` site transient
5. call `wp_update_plugins()`
6. redirect back to the Plugins screen

The updater's forced-check detection must recognise its own plugin-row query key as well as WordPress's native forced-check request shapes. This ensures the manual action cannot reuse a plugin-specific cached release response.

After `wp_update_plugins()` returns, the handler must read the rebuilt `update_plugins` site transient, inject the plugin's GitHub update data once more, and persist the result. This avoids losing the custom update entry when another update provider rewrites the transient during the same refresh.

The redirect must carry a short result code and the Plugins screen must display a dismissible administrator notice confirming one of these outcomes:

- an update is available
- the installed version is current
- the GitHub check failed

A manual check must not fail silently. Error notices should remain generic and must not expose raw remote-response bodies, internal diagnostics, HTTP status text, or GitHub rate-limit messages.

This link must trigger WordPress's native plugin update mechanism and return the user to the Plugins screen. It must not redirect users to `update-core.php` as the final destination, directly update plugin files, or require users to visit GitHub.

This link is part of the required WordPress-native update workflow. After the check completes, WordPress should show the native update prompt and "update now" action when a newer GitHub release exists.

WordPress must surface updates through the native plugin update UI.

Inactive plugins cannot add dynamic row metadata or run their self-contained updater code. For plugins that must be updateable from Network Admin while otherwise unused on the main site, the plugin should support network activation safely so its updater is loaded in Network Admin while feature behaviour remains gated per site as needed.

Do not use a static `Plugin URI` as a fallback. Repository discovery belongs in the updater's explicit GitHub row metadata link; update discovery and installation belong in WordPress's native update UI.

If an update does not appear immediately after publishing a release, use WordPress's native update mechanisms, including Dashboard > Updates > Check again or the plugin row "Check for updates" link.

---

## Update Check Timing

The updater should run as part of WordPress's normal plugin update checks.

Recommended behaviour:

- use the `pre_set_site_transient_update_plugins` filter
- use the `site_transient_update_plugins` filter as a read-path safety net
- cache GitHub release lookups with a plugin-specific site transient
- keep newer-version successful lookups cached for about 6 hours
- keep equal-version or older-version successful lookups cached for only 1-15 minutes
- do not store failed lookups in the release transient
- store failed lookup diagnostics separately only when useful
- apply a short failure backoff after HTTP, DNS, SSL, JSON, or rate-limit failures so ordinary admin requests do not hammer the remote endpoint
- bypass the plugin-specific release cache when WordPress is performing a forced update check
- clear the plugin-specific release cache and any diagnostic transient after successful plugin updater runs
- let WordPress decide when to refresh plugin update data

Do not call GitHub on every admin page load.

Do not rely on the WordPress `update_plugins` transient alone. A plugin-specific release transient that cached "latest release equals installed version" before a new GitHub release was published can keep hiding the update unless it is short-lived or bypassed during forced checks.

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
4. Update the root `update.json` manifest when the plugin uses manifest-first discovery.
5. Confirm the plugin header declares its GPL-compatible licence and the package contains `LICENSE`.
6. Update `readme.txt` and confirm its Stable tag matches the plugin version.
7. Add release notes to `CHANGELOG.md`.
8. Run available syntax/tests/build checks.
9. Build the plugin ZIP.
10. Verify the ZIP top-level folder and required licence/readme files.
11. Commit the source changes.
12. Push to GitHub.
13. Create a GitHub release tag matching the version.
14. Attach `plugin-slug.zip`.
15. Verify the release asset is visible.
16. Clear or bypass the plugin-specific GitHub release cache on a test site.
17. In WordPress, use Dashboard > Updates > Check again.
18. In WordPress, confirm the plugin row has **GitHub** and **Check for updates**, and does not have **Visit plugin site**.
19. Confirm the update is offered on the Plugins page.
20. Confirm WordPress installs the update.

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
- plugin header declares a WordPress-compatible GPL licence
- distributable plugin contains `LICENSE` and a WordPress.org-format `readme.txt`
- `readme.txt` Stable tag matches the plugin version
- plugin header omits `Plugin URI`, so WordPress does not render **Visit plugin site**
- active plugin row includes **GitHub** and a nonce-protected **Check for updates** link for users who can update plugins
- changelog has the new version
- ZIP contains the plugin folder at the top level
- ZIP does not include unrelated repository files
- GitHub release tag matches the plugin version
- GitHub release includes the expected ZIP asset
- root update manifest matches the plugin version when manifest-first discovery is used
- the GitHub latest release API returns the new tag and expected asset
- plugin-specific stale release cache has been cleared or bypassed during testing
- WordPress detects the update from the Plugins page
- "View details" opens useful release information
- update install succeeds on a test or production site as appropriate
- failed GitHub HTTP, SSL, DNS, or JSON responses do not write `no_update`
- failed GitHub HTTP, SSL, DNS, or JSON responses do not write an error sentinel into the release transient
- any updater diagnostics are stored separately from release data
- a valid manifest check does not also call the GitHub API
- HTTP 429 responses activate short backoff and do not expose raw GitHub error text in WordPress

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
- plugin-specific GitHub release transient has been cleared or bypassed by a forced update check
- plugin-specific release transient is not caching an equal-version release for several hours
- plugin-specific release transient does not contain an error sentinel such as `asig_error`
- failed GitHub lookups are not being cached as release state
- updater hooks both `pre_set_site_transient_update_plugins` and `site_transient_update_plugins`
- forced update detection recognises the current WordPress update request shape
- the WordPress server can reach `https://api.github.com/repos/{owner}/{repo}/releases/latest`

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

Failed GitHub requests must not be stored in the release transient used for update decisions.

Good pattern:

```php
if (is_wp_error($response)) {
    set_site_transient(
        self::ERROR_TRANSIENT,
        array(
            'type'       => 'wp_error',
            'message'    => $response->get_error_message(),
            'checked_at' => time(),
        ),
        10 * MINUTE_IN_SECONDS
    );
    delete_site_transient(self::RELEASE_TRANSIENT);
    return false;
}

$response_code = wp_remote_retrieve_response_code($response);

if (200 !== $response_code) {
    set_site_transient(
        self::ERROR_TRANSIENT,
        array(
            'type'       => 'http_error',
            'code'       => $response_code,
            'message'    => wp_remote_retrieve_response_message($response),
            'body'       => substr(wp_remote_retrieve_body($response), 0, 500),
            'checked_at' => time(),
        ),
        10 * MINUTE_IN_SECONDS
    );
    delete_site_transient(self::RELEASE_TRANSIENT);
    return false;
}
```

Bad pattern:

```php
set_site_transient(self::RELEASE_TRANSIENT, array('asig_error' => true), 30 * MINUTE_IN_SECONDS);
```

That stores a failed lookup where valid release data belongs and can block update notices until the cache expires.

For hotfix-sensitive sites that want immediate update availability across multiple GitHub-distributed plugins, use a site-level MU plugin to clear WordPress's `update_plugins` transient on update screens. Plugin updaters must still avoid caching failed lookups as release state.

---

## Codex Maintenance Rule

When Codex changes a plugin that uses this standard, Codex must:

- remove any existing `Plugin URI` header in the next release and verify **Visit plugin site** is absent from the Plugins screen
- update the version number
- update the version constant
- update release notes
- rebuild the plugin ZIP if publishing a release
- push source changes
- create the GitHub release when asked or when release publishing is in scope
- verify the release asset exists
- tell the user whether PHP lint/tests were run

If PHP is not available locally, Codex must say so in the final response.
