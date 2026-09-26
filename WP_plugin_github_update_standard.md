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

## Controller Ownership

Each author brand has its own update coordinator and catalogue:

| Author | Controller display name | Official repository | Client API |
| --- | --- | --- | --- |
| Techn | TN Update Controller | `cchatterton/tn-update-controller` | `tnuc_`, `TNUC_API_VERSION` |
| AlphaSys | AS Update Controller | `cchatterton/as-update-controller` | `asuc_`, `ASUC_API_VERSION` |

Techn's catalogue contains only approved Techn-authored plugins from the specified GitHub owner; AlphaSys's contains only approved AlphaSys-authored plugins. Verify authorship from the published plugin package, not a repository-name prefix. Both controllers may coexist with independent namespaces, options, locks and schedules. Neither registers, updates or displays the other brand's catalogue. Each controller includes itself.

UI bootstrap actions use **Install Techn Update Controller** / **Activate Techn Update Controller**, or **Install AlphaSys Update Controller** / **Activate AlphaSys Update Controller**, according to the client author. The remaining requirements apply independently to both controllers.

Individual plugins declare their identity and expose a small, guarded controller integration. They must not contain independent release discovery, remote update checks, update cron jobs, or fallback updaters. The controller manages its own updates using the same discovery and caching rules.

This standard specifies the target architecture; it does not imply that the controller has already been released. Existing sites migrate through the process below. Do not publish a migrated plugin with a broken controller-install link: first release and verify the controller and its supported bootstrap contract.

The controller is required for managed update discovery, not for ordinary plugin feature operation. Plugins must continue to work when it is absent or deactivated. Do not introduce a hard activation dependency solely for update management.

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
Catalogue ID:
Controller integration contract version:
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
Catalogue ID: example-plugin
Controller integration contract version: documented by the controller release
```

---

## Repository Visibility

The default distribution uses public repositories and publicly downloadable release ZIP assets, without requiring a GitHub token on WordPress sites.

Private distribution requires an explicit specification and authenticated catalogue/package delivery. Never embed private credentials in plugin packages, catalogue metadata, browser links, or client-side code.

---

## Plugin Header Update Source

Every GitHub-distributed plugin must include an `Update URI` header and must omit the `Plugin URI` header.

WordPress automatically renders `Plugin URI` as a **Visit plugin site** link on the Plugins screen. Do not declare `Plugin URI`; the GitHub and native update links supplied by the controller integration are the approved discovery and update paths.

When maintaining an existing plugin that still declares `Plugin URI`, remove that header in the next plugin release. Treat this as required release maintenance even when the feature change is otherwise unrelated. Confirm the resulting Plugins screen no longer shows **Visit plugin site** for that plugin.

The plugin header must use `Author: Techn` and `Author URI: https://techn.com.au` for Techn products. Do not use `TECHN` or alternate casing in author text. AlphaSys products retain their own author identity.

Keep `Update URI` as the stable update-source identifier and protection against accidental WordPress.org slug matching:

```text
Update URI: https://github.com/{owner}/{repo}
```

`Update URI` is distinct from `Plugin URI`; omitting the latter must not remove the former. The header alone does not implement an updater.

The expected plugin row is `Version … | By Techn | GitHub | <controller action>`. The controller-state action is specified under **Plugin Row Metadata** below. Native **View details** and **Update now** actions remain available where applicable.

---

## Version Discipline

Every completed change that alters the distributable plugin package, plugin metadata, runtime behaviour, admin behaviour, front-end behaviour, or bundled assets must be released through WordPress. A request to change such a plugin is a request to complete the release unless the user explicitly asks for a draft, local-only work, review, or no release.

The release must include:

1. A plugin header version bump.
2. A matching plugin version constant bump.
3. A changelog entry.
4. A GitHub release tag matching the version.
5. A root-level release ZIP with the expected filename.
6. A GitHub release ZIP asset with the expected filename.

Example plugin header:

```php
/**
 * Plugin Name: TN Example Plugin
 * Description: Short description.
 * Version: 0.1.4
 * Requires at least: 7.0
 * Requires PHP: 7.4
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

Use the next valid semantic version after inspecting both the current source version and published GitHub releases. Use a patch increment for backward-compatible fixes by default, and a minor or major increment when the requested scope or repository policy requires it. Never reuse, move, or overwrite an existing release tag.

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

## Update Discovery and Performance

**Displaying update information must never fetch update information.**

The controller must make zero outbound update-metadata HTTP calls while rendering ordinary admin or front-end pages, including Plugins, Dashboard, the controller catalogue, plugin rows, notices, and the View details modal. This applies with warm, cold, expired, missing, or evicted caches and when remote services fail.

Update-transient read/write filters, `plugins_api`, and update-provider callbacks may only project locally stored metadata. They must not perform HTTP calls, recursively refresh transients, delete caches, or invoke `wp_update_plugins()`. A cache miss means unknown/stale status until a separate check completes, not permission to fetch on the read path.

Remote discovery is permitted only in a scheduled worker or an explicit authorised check operation, separate from page rendering. Installation/package downloads are distinct authorised operations and use the WordPress upgrader.

### Scheduling and manual checks

- Default to background discovery approximately every six hours, with modest scheduling jitter between sites. Use the same interval whether the installed version is current or older.
- Offer a manual-only setting. In this mode, do not schedule automatic discovery; explain that new releases remain unknown until a manual check succeeds.
- Provide per-plugin **Check for updates**, **Check all Techn updates**, and **Refresh catalogue** actions. The last action refreshes catalogue metadata only and does not install anything. AlphaSys uses equivalent actions within its own controller; never combine author catalogues.
- Validate capabilities and nonces in every action endpoint. A row action targets one recognised plugin; an aggregate catalogue may be fetched once to satisfy it, but must not trigger unrelated per-plugin lookups or installations.
- A manual action may bypass the normal freshness interval once. It must not bypass an in-progress job, deduplication, or remote retry deadlines. Repeated clicks reuse/report the existing operation.
- Return promptly with a job identifier and visible progress when work is queued. Provide a bounded explicit check path when cron is unavailable; never silently fall back to checking during page rendering.
- Register schedules once, reconcile changes to the scheduling setting, and remove the controller's jobs on deactivation. Verify scheduler health and show delayed/failed checks with a recovery action.
- Native WordPress **Check again**, if integrated, must dispatch one controller check through a verified action adapter. Never interpret a global `force-check` query value inside metadata getters as permission to ignore caching.
- `update-selected`, `upgrade-plugin`, and `do-plugin-upgrade` are installation operations, not triggers to rediscover every plugin.
- Never add `techn_update_refresh` or `force-check` to ordinary Plugins navigation. After explicit actions, redirect to a clean URL with only a non-repeating result reference where needed.

### Stored state, deduplication, and failures

Keep the last successful release/catalogue snapshot separately from refresh scheduling and diagnostics. Use durable WordPress options/site options for the last-known-good snapshot; an expiring transient alone must not be its only copy. Temporary locks and caches may use suitable WordPress storage.

Track at least `last_attempt`, `last_success`, `next_check`, retry deadline, and current job/result state. Show **Never checked**, **Current as of …**, **Update available**, **Check failed; showing previous results**, or **Check in progress** accurately. A failed lookup must never be recorded as a successful no-update result.

- Preserve the last successful metadata on HTTP, DNS, TLS, JSON, or schema failure. Do not overwrite it with an error sentinel or delete it merely because a refresh failed.
- Validate a complete candidate snapshot before replacing the last valid snapshot. Do not accept malformed or untrusted package identities.
- Deduplicate each source lookup within a job/request, including failed lookups and forced checks. Share the result between all consumers.
- Use an atomic lock scoped to the site/network and job source, with ownership, expiry, and recovery after interruption. A non-atomic read-then-set transient is not sufficient to prevent concurrent workers.
- Apply bounded HTTP timeouts, response-size limits, and bounded worker batches. Specify and test the chosen budgets in the controller implementation; no unbounded retry loops or serial page-render fetches are permitted.
- Treat `429` and verified rate-limit `403` responses as retryable failures. Honour `Retry-After` and rate-limit reset headers, use increasing backoff with jitter, and coordinate throttling by affected remote service. Manual checks do not clear this protection.
- Other lookup failures also require backoff, initially at least ten minutes unless the provider requires longer. Reset failure state after a successful check.
- Show useful, sanitised administrator status and retry timing. Keep sensitive response bodies, credentials, and internal stack traces out of notices.

---

## Catalogue and Release Metadata

Maintain an explicit registry of supported plugins. Match installed plugins by their known plugin basename (directory/main PHP file) and configured identity, including repository and Update URI where present. Legacy entries may specify audited aliases or missing old headers. Never claim ownership using author text or a name prefix alone. Ambiguous or unknown installations require review and must not be overwritten automatically.

The registry must include the controller itself and cover installed inactive plugins without loading their feature code. Discover installed headers using WordPress APIs.

Each entry must define:

- stable catalogue ID, plugin basename, slug, author, display name, repository owner/name and expected ZIP asset name
- published version/tag, release summary/changelog, and release details location
- WordPress/PHP requirements and declared dependencies
- controller integration requirements and known legacy identifiers where applicable
- catalogue description and optional approved artwork

Use a versioned, validated catalogue schema and a trusted, configured HTTPS source. Construct package URLs from trusted repository identity, validated release tag, and configured asset filename. Do not accept arbitrary package URLs or browser-supplied download destinations. Preserve TLS validation and restrict redirects to expected release delivery hosts.

The preferred steady-state design is one aggregate published catalogue: one successful metadata request per refresh job, then local version comparison for all plugins. Do not send site inventory or credentials to fetch a public static catalogue.

An initial controller may use repository `update.json` manifests in bounded background batches, at most one lookup per selected source per job. This is a transitional transport, not a reason to put fetching back into individual plugins. Valid manifests must not trigger GitHub API enrichment, `/releases/latest` redirect probes, or package probes. Missing/invalid metadata should produce a recorded failure and backoff; do not implement automatic multi-endpoint fallback chains on WordPress sites.

Use GitHub API access in the release publishing/validation workflow when needed, rather than relying on unauthenticated API quotas across the installed fleet.

### Publish after the package exists

Only advertise a stable release after its tag, published GitHub release, and expected downloadable ZIP have been verified. Exclude drafts and prereleases from the stable catalogue.

If a root `update.json` remains a live discovery endpoint, publish its new version after asset verification, in a follow-up metadata commit. Prepared metadata can be staged elsewhere before release; do not advance a live manifest with the initial version-bump commit. Aggregate catalogue publication must likewise follow asset verification and preserve concurrent release entries.

The advertised version, release tag, ZIP contents, header, version constant, readme Stable tag, and displayed version must agree for that released package. Keep the previous published catalogue entry if package verification or catalogue publication fails, and report delivery as incomplete.

Do not probe release ZIPs with `HEAD` or `GET` during ordinary discovery. Asset verification belongs in release publishing; actual installation downloads belong to the WordPress upgrader. Do not store expiring signed redirect URLs as durable package locations.

---

## WordPress Update Integration

The controller is the sole update provider for its registered plugins, including itself. It must preserve native WordPress update badges, plugin update rows, View details, individual updates, and bulk updates, while retaining entries belonging to other providers.

Use supported WordPress update APIs and filters. `pre_set_site_transient_update_plugins`, `site_transient_update_plugins`, and `update_plugins_{$hostname}` are possible integration points, not a requirement to attach duplicate handlers. Whichever are used must be idempotent, local-data-only projections. Do not intercept unrelated GitHub-distributed plugins merely because they share a hostname.

Validate transient shape before modifying it. Populate the managed plugin's update response with its identity, slug, plugin basename, new version, trusted package/release URLs, and compatibility requirements. Strip a leading `v`/`V` from tags and compare validated versions with `version_compare`, never plain string ordering.

Remove a managed plugin's stale update entry when its installed version catches up with the successfully discovered version. A valid successful comparison may populate `no_update`; failure must not create a false no-update result or erase a known available release. Unknown plugins and third-party update entries must remain unchanged.

Do not delete the global `update_plugins` transient to check one plugin. After a discovery job succeeds, reconcile managed entries once without triggering remote work in transient callbacks. After installation, re-read the installed version and reconcile locally; do not discard the whole catalogue or retry history.

Use WordPress's native installation and upgrade APIs. Respect platform file-modification restrictions, filesystem credentials, compatibility requirements, capabilities, and multisite rules. Do not directly replace plugin files with custom filesystem code. Checking for updates does not enable auto-updates or install anything; preserve the site's existing auto-update choices.

Serve View details from cached catalogue/release information with a useful description, author, version, compatibility and changelog. With no metadata available, show an honest unavailable/check-needed state instead of fetching synchronously.

### Multisite

Use one network-scoped controller catalogue, schedule, and lock for shared plugin files. Require network-level update/install authority and perform shared-file operations in Network Admin. Detect site-active and network-active controller states correctly; a controller active on a subsite alone must not be treated as the network's update provider. Preserve site and network activation scope during migration. Do not network-activate feature plugins solely to make update discovery work.

---

## Plugin Row Metadata

Use exact author spelling **Techn**, with **By Techn** linking to `https://techn.com.au`. Preserve **AlphaSys** for AlphaSys products. The **GitHub** link targets that plugin's official repository. Omit `Plugin URI` and therefore **Visit plugin site**; retain `Update URI`.

Expose one controller action according to local installation/runtime state. The table below shows Techn labels; AlphaSys substitutes **AlphaSys** for **Techn** and uses its own controller:

| Controller state | Exact row action | Behaviour |
|---|---|---|
| Not installed | Install Techn Update Controller | Opens the trusted controller installation flow |
| Installed but inactive in the required context | Activate Techn Update Controller | Opens/performs the authorised native WordPress activation flow |
| Active and compatible | Check for updates | Calls the matching author's controller; a single aggregate catalogue fetch may cover all its registered plugins |
| Installed but incompatible | Update Techn Update Controller | Opens the controller's native update or documented recovery flow |

Only show executable actions to users with the corresponding capability (`install_plugins`, activation authority, or `update_plugins` as appropriate), and verify permissions/nonces server-side. Users without permission may see concise status, never an action they can execute without authority.

Example when the controller is absent:

```text
Version 1.2.3 | By Techn | GitHub | Install Techn Update Controller
```

Example when it is active:

```text
Version 1.2.3 | By Techn | GitHub | Check for updates
```

A migrated active plugin supplies only a small, namespaced, guarded bootstrap integration for author/repository metadata and controller-state actions. It must use the controller's published versioned contract and local detection, independent of plugin load order. Multiple integrations must not register duplicate links, handlers, or installers. When active, the controller owns row actions for recognised active and inactive plugins; feature plugins yield to it.

The bootstrap may resolve/download the controller only after an explicit authorised installation action, using a trusted controller source and native WordPress installer. It must not become a fallback updater for the feature plugin, and must not resolve the controller's latest version while rendering a row. The controller implementation must publish and test its exact basename, bootstrap source, detection/API contract, and minimum supported version before clients ship against it; do not guess a repository URL or invent a WordPress.org listing.

When the controller is absent, inactive plugins cannot run their bootstrap links. Document the direct controller installation route for this case. Installation/activation of the controller must not automatically update all other plugins; offer the setup workflow below. Normal feature operation continues if controller setup is postponed.

---

## Controller Admin Experience

Provide **Techn Plugins** or **AlphaSys Plugins** under the WordPress Plugins menu, using the appropriate Network Admin location on multisite. Use Author Branded mode with the matching author identity and native WordPress controls. Each page contains only its own approved author catalogue.

| Tab | Required purpose |
|---|---|
| Installed | Installed/available versions, active/inactive state, compatibility, last successful check, migration status, per-plugin check and bulk update selection |
| Catalogue | Searchable plugin cards with description, approved artwork where available, version, requirements, details and state-appropriate actions |
| Settings | Scheduled/manual-only checking, interval, scheduler status and sanitised diagnostics |

Catalogue card actions must reflect actual state: **Install**, **Activate**, **Open settings** where available, or **Update**. Explain incompatibility and disable the affected action. Installation must not silently activate a feature plugin. Retain native Deactivate controls on the Plugins screen; the catalogue need not duplicate every management action.

Show cached catalogue content immediately. With no snapshot, provide an empty state and explicit **Refresh catalogue** action. Never refresh per card or fetch metadata as a side effect of opening a tab. Batch progress/status polling locally and stop it when the job completes. Make loading, partial failure, stale data, permission limits and retry states keyboard accessible and understandable without colour alone.

Individual feature settings pages may show their installed version and link to this controller page; do not reproduce updater settings in every plugin. Keep native WordPress notifications and Update now actions available alongside the catalogue.

---

## Legacy Migration and Site Rollout

Repository migration and site migration are separate deliverables. A standards change or controller installation alone does not disable deployed legacy updater code.

1. Release and verify the controller, its catalogue and bootstrap contract first.
2. Publish a migration release for each registered plugin: remove independent updater hooks/jobs, force-refresh redirects and remote lookups; add the small controller integration; standardise author/link metadata. Preserve existing plugin basenames, slugs, options and feature behaviour unless an explicit migration handles a required change.
3. Publish catalogue entries only after those packages are verified.
4. Test the complete old-to-new migration on staging, then deploy the controller once per remaining site/network using existing authorised deployment tooling or manual installation.

The controller must recognise audited legacy basenames so old plugins do not first need an individual manual upload. Setup must:

- inspect installed registered plugins, active and inactive, without executing inactive plugin code
- offer an explicit discovery check and show installed-to-target versions, compatibility, release notes, known migration limitations and scope
- provide **Update selected plugins** as an explicit action; activation alone must not replace feature plugins
- update through WordPress in bounded, resumable batches with progress and per-plugin outcomes, respecting dependencies and not automatically downgrading newer/local versions
- preserve active/inactive and site/network activation state, stored settings and unrelated plugins
- recheck installed versions, verify supported migration completion, and offer individual retries after failure
- record actual completion per plugin/version; do not repeatedly run a one-time migration on every activation or page load

A bulk migration is not an atomic transaction. Report partial completion accurately and give recovery instructions; do not promise automatic rollback of the whole site. Validate backup/recovery arrangements before a production rollout.

Legacy compatibility handling must be narrowly scoped to audited plugin versions/hooks and removable after migration. Test both load orders and inspect site-level MU plugins that may force update checks. Never disable all HTTP requests, remove all update filters, or claim arbitrary legacy updaters have been suppressed. Report unknown/conflicting implementations as requiring attention. A controller that runs alongside unchanged legacy updaters has not completed migration.

Keep the controller's own update/recovery path functional. Document trusted manual ZIP replacement if it cannot run. Individual plugins must not resume their old updaters if the controller is disabled.

---

## Release Process

For each distributable plugin change:

1. Implement the change and applicable controller integration/migration requirements.
2. Bump the plugin header and canonical version constant using the existing version discipline.
3. Update `CHANGELOG.md` and `readme.txt` Stable tag. Confirm licence headers and `LICENSE`.
4. Run syntax, behaviour and applicable controller compliance checks.
5. Build and verify the ZIP, including top-level folder, main file, version, licence/readme, and absence of nested ZIPs/development artifacts.
6. Commit/push source and the generated root ZIP. Do not advance a live discovery manifest yet.
7. Create the matching immutable release tag at the verified source commit. Publish the release with the exact expected ZIP and matching release notes.
8. Verify the remote commit/tag, published release, asset name, download and packaged version. Do not reuse tags or overwrite an existing version's asset.
9. Publish the live repository manifest, where used, and aggregate catalogue entry only after step 8. Verify the published discovery data resolves to that verified release. Report partial delivery if this step fails.
10. On a test site, use the controller's explicit check; confirm the native update notice and View details, then install through WordPress and verify the resulting version and feature operation.
11. Confirm the correct controller-state row action and absence of Visit plugin site. Measure ordinary admin loads with cold/warm metadata and check for zero updater discovery requests.

The task is not complete until the source commit, tag, published release, ZIP and applicable discovery metadata are verified. WordPress-side checks may be reported as pending only when the required environment/access is unavailable. State the exact unfinished checks.

Repository-only documentation, tests or tooling changes that do not affect the distributable package must still be committed and pushed, but do not need a plugin version bump or product release. Explicit draft/local-only/no-release instructions override the default delivery requirement. Complete safe available steps and report external blockers without presenting partial delivery as complete.

---

## Compliance and Verification

Controller releases must include meaningful automated tests for these behaviours. Client releases must validate their integration and package identity; migration releases must also test removal of legacy updater behaviour. Static lint alone is insufficient.

- Repeated update-transient reads/writes, plugin rows, View details and ordinary pages cause **zero update-metadata HTTP requests**, including cold/expired/evicted caches and remote outages.
- Successful aggregate discovery performs one metadata lookup per job; transitional per-repository discovery performs at most one per selected source. Repeated callbacks, manual clicks and concurrent workers do not duplicate work.
- `force-check` and install-action query parameters do not bypass caches in getters or create navigation/refresh loops. Redirects do not repeat the operation.
- Timeouts, bad JSON/schema, `403` and `429` preserve valid metadata and enforce retry deadlines. No failure becomes Current/no-update. Manual checks respect backoff and locks.
- Manual-only mode has no automatic discovery schedule. Scheduled mode deduplicates jobs and handles disabled/delayed cron with visible status and recovery.
- Controller absent, inactive, active, incompatible and unavailable-download states show accurate actions, including permission/nonce failures and both load orders.
- Active/inactive registered plugins receive native update metadata; unrelated plugins/providers and global update information remain intact.
- Installation/bulk updates respect platform restrictions, compatibility, dependencies, authorisation and multisite scope. Unknown identities and untrusted package destinations are rejected.
- Guided migration preserves plugin settings and activation state, reports partial failure, resumes safely, and removes supported legacy updater behaviour. Installed versions newer than the catalogue are not downgraded.
- The controller updates itself and has a documented recovery route. Client functionality survives its absence/deactivation.
- Catalogue Installed/Catalogue/Settings tabs cover empty, cached, stale, loading, incompatible, permission-restricted, success and partial-failure states using accessible native controls.
- No catalogue entry advertises an unverified/missing release asset. Release metadata and package versions match; package downloads are never used as discovery probes.
- Query Monitor on representative Dashboard and Plugins loads confirms no updater metadata calls. Record before/after timings, separate WordPress/third-party HTTP activity from controller activity, and do not claim zero total HTTP for unrelated providers.

For each plugin release also verify PHP lint/tests as available, licence/readme, changelog, author spelling, row links, package structure, version consistency, remote commit/tag/asset and actual update installation when access permits.

---

## Troubleshooting

### An update is missing

Check controller state, catalogue identity/basename, last successful check, advertised version, compatibility, release publication, and pending/backed-off jobs. Use an explicit controller check. Do not flush global transients or add an MU-plugin refresh-on-page-load workaround.

### An update installs but the plugin disappears

Verify the expected basename and ZIP root: `plugin-slug/plugin-slug.php`, not a bare main file or a renamed directory.

### The remote service is unavailable

Keep normal plugin operation and the last successful snapshot. Show stale/failed status and the next allowed retry. Do not resurrect independent updaters or retry from rendering callbacks.

### Admin remains slow after controller installation

Inspect HTTP callers and identify legacy plugin/MU-plugin force-refresh code. Confirm migration versions are actually installed and the legacy hooks are no longer executing. Do not assume installing the controller fixes old implementations automatically.

---

## Codex Maintenance Rule

When maintaining a plugin under this standard, use the controller contract and migration process rather than copying a self-contained updater. Do not invent controller endpoints before the controller is available. Preserve package identity, correct author/link metadata, complete the applicable version/changelog/build/push/release/catalogue steps, and report the released version and verified URL.

For documentation-only changes, commit and push without an unnecessary plugin release. Report which checks ran, which runtime checks remain pending, and any deployment limitations. Updating this standard does not itself release the controller, migrate plugin repositories, or modify installed sites.

## Catalogue beta status and grouping

Every trusted registry entry must declare an explicit boolean `beta` status. New or unreviewed entries default to beta. Remove beta only after completing and validating the standards migration; do not infer readiness from the author, version number or controller API header alone. Publish the status in verified catalogue metadata so an approved promotion can reach sites during their next catalogue check. Older cached catalogues without this field use bundled defaults; reject malformed status values.

Render catalogue groups in this order: Active; Installed (installed but inactive); Available (uninstalled non-beta); Beta (uninstalled beta). A plugin appears only once. Activation and installation take precedence over readiness. Active and installed beta plugins retain their Beta chip. Active cards offer Deactivate; Installed cards offer Activate and Delete; Available and Beta cards offer Install. Keep update actions on Updates available. Search spans all groups and hides empty sections. Refresh card state through an authenticated, nonce-protected server response after lifecycle actions; do not rely on cached page HTML. Beta is a readiness label, independent of GitHub prerelease channels, permissions, auto-update preferences and compatibility checks.

## Alpha promotion, domain availability and catalogue actions

Alpha is the reviewed, non-beta readiness state. Complete applicable standards corrections and validate controller integration, compatibility, representative feature behaviour, package identity and the published release before promoting an entry (`beta: false`). The label is not a claim that every external integration has been exercised; record the actual test scope and limitations. Unresolved compliance gaps remain beta.

An optional trusted-registry `allowed_domains` array contains lower-case DNS hostnames only; `include_subdomains` explicitly enables matching descendants at a dot boundary. An absent or empty list means unrestricted availability. Match the configured home URL on single-site and the network home URL on multisite, never a request Host header. A mixed-domain network is governed by its network domain; a qualifying member site alone does not authorise network-wide installation. Rules are approved in a controller release and remote metadata cannot expand them. Apply restrictions to catalogue cards, update projections, install/update validation and package downloads, including cold caches. A public GitHub asset remains public: catalogue availability is not download authentication or a licensing mechanism.

The first controller tab is Updates available and contains only installed, verified identities with newer releases. The catalogue holds the full permitted library. Cards expose permission-checked activation/deactivation and confirmed deletion instead of Manage plugin. Network admin actions explicitly use network scope. Deletion must respect file-modification policy and must fail while a plugin is active on any member site, without silently deactivating it. Use native WordPress lifecycle APIs and dependency checks, nonce-protected requests, an operation lock, verified completion and accessible feedback. Self-deactivation uses the native Plugins action so the controller can unload cleanly. Keep Beta chips small at the bottom-right of catalogue cards.
