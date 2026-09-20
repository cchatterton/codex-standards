# Salesforce Unmanaged Package Standard

## Purpose

This standard defines how AlphaSys and Techn Salesforce unmanaged packages are developed, documented, installed, and maintained. Apply it with `Codex_development_standards.md`, `Branding_and_UX_standards.md` for administrator-facing work, and the project-specific specification. The project specification takes precedence.

An unmanaged package is an **initial distribution of editable Salesforce metadata**. It is not an in-place upgrade mechanism. Plan later changes as controlled metadata deployments to each installed org, with their own validation and release record. Do not copy the WordPress GitHub ZIP updater workflow into Salesforce.

## Default Development Org

Use this Salesforce Developer Edition org as the default development and validation org for AlphaSys and Techn unmanaged package work, unless a project-specific specification names another org:

- Org URL: `https://orgfarm-83452b405d-dev-ed.develop.my.salesforce.com/`
- Developer username: `chris.48138b0da64f@agentforce.com`

These are org identifiers, not authentication secrets. Keep passwords, access tokens, and other secrets out of this repository. Before any deployment, confirm the authenticated CLI or browser session points to this org rather than relying on a remembered default.

## Project Declaration

Before development, record:

- Author brand: AlphaSys or Techn.
- Package name, repository, and component prefix.
- Supported Salesforce editions and minimum API version.
- Target orgs and who owns their configuration and deployments.
- Which components are packaged, which are created during setup, and which are customer-owned.
- Any external systems, endpoints, credentials, and permissions the package requires.

Do not use Fusion as the default author or prefix unless the project specification asks for it. Use a short, distinctive prefix consistently for package-owned objects, fields, Apex classes, Lightning components, permissions, and configuration. Follow Salesforce API-name rules and check for collisions in target orgs.

## Source and Package Contents

- Keep Salesforce metadata in source control in Salesforce DX source format. Treat the repository as the development source, not an installed org.
- Keep each component focused. Separate record selection, business rules, transport, persistence, and admin presentation.
- Include only the metadata needed to install the specified feature. Do not package customer records, secrets, environment-specific URLs, or unrelated org customisations.
- Prefer Salesforce-native metadata and interfaces. Use custom objects or custom metadata only when their different roles are clear: operational records versus configuration.
- Document package-owned metadata, its purpose, dependencies, and what an installer may edit.
- Give permission sets the access the feature actually needs. Document any permissions that must be assigned after installation.

## Apex and Data Operations

- Use bulk-safe Apex. Process collections rather than querying or writing once per record.
- Keep triggers small and route their work into focused services. Avoid synchronous network calls from record-save paths.
- Make scheduled and asynchronous work bounded, observable, and safe to run again after interruption.
- Validate dynamic object and field names against Salesforce describe information and the saved configuration before using them in a query or write.
- Apply the intended sharing and CRUD/FLS policy deliberately. Document any system-context operation and why it is required.
- Never assume that installing a package gives it a trigger on an arbitrary object selected later by an administrator. Specify and verify the change-detection mechanism for each supported object.
- Keep durable identity and audit records separate from temporary work queues when the project needs both.

## Administrator Experience

- Use Salesforce Lightning patterns and accessible components. Follow `Branding_and_UX_standards.md` without making the screens feel foreign to Salesforce.
- Show configuration, current work, completed work, and errors in terms an administrator can act on.
- Make scheduled work visible: last run, next expected run, queue size, oldest item, and failures where relevant.
- Require appropriate administrative permissions for setup and manual actions. Confirm destructive operations in the UI.
- Do not hide installation steps, missing field access, or incomplete external connections behind a generic success message.

## Integrations and Secrets

- Use Salesforce-supported authentication and credential storage for outbound connections; do not hardcode secrets in Apex, metadata, tests, or repository files.
- Validate external responses before changing local records. Keep request and response formats versioned when another component depends on them.
- Distinguish receipt of a message from completion of its work when the integration is asynchronous.
- Record enough identifiers and timestamps to trace an item across systems without logging credentials or unnecessary personal data.
- Document the relevant API, callout, asynchronous-job, and storage limits for the target org. Make batch sizes and schedules consistent with those limits.

## Installation and Configuration

- Provide an installation guide with prerequisites, package link or artifact, required permissions, and post-install steps.
- Identify package components that depend on metadata already present in the target org. Check those dependencies before enabling the feature.
- Keep environment-specific settings out of the distributable package where practical. Explain how to configure them safely after installation.
- If setup creates or changes metadata on an administrator-selected object, state the required permissions, show the proposed change, and report deployment success or failure. Removing a configuration must not silently delete customer data or fields.
- Verify installation and configuration in a representative test org before production use.

## Tests and Validation

- Add meaningful Apex tests for the business rules, permissions, bulk operations, and expected failure paths. Use callout mocks for external requests.
- Run the required Salesforce tests and validate the metadata deployment against a target-like org before release.
- Exercise installation, the post-install checklist, administrator screens, and at least one complete operational flow in a test org.
- Check that existing customer configuration and data survive any later deployment.
- Record what was tested, the org or environment used, and any unverified behaviour. Do not describe a metadata syntax check as an integration test.

## Versioning and Maintenance

- Record a package version, source commit, release date, and changelog entry for each distributed version.
- Tag the source commit and preserve the exact installation artifact or package link used for that version.
- Publish clear installation and release notes, including new setup steps and compatibility changes.
- **Do not present a new unmanaged package version as an upgrade for an installed org.** Salesforce does not update existing installed components through an unmanaged package installation. Deliver subsequent changes through a reviewed metadata deployment, or explicitly choose a package type that supports upgrades.
- Before a metadata deployment, compare target-org customisations with the intended change, assess data and configuration impact, validate in a test org, and retain a recovery plan.
- Confirm the deployed component versions and required configuration in the destination org; a GitHub tag alone does not prove that an org was updated.

## Delivery Checklist

Before calling the work complete, confirm:

1. The project declaration and component inventory are current.
2. Source, metadata, tests, and documentation match the specification.
3. Security, sharing, permissions, and credentials have been reviewed.
4. Required tests and deployment validation passed in a representative org.
5. The package installs, or the planned metadata deployment succeeds, and post-install configuration works.
6. Version, changelog, source commit, distribution artifact, and deployed org state are recorded.
7. Known limitations and any manual setup or recovery steps are reported.
