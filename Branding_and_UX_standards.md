# Branding and UX Standard

## Purpose

This standard defines how AlphaSys and Techn interfaces should express brand quality and deliver a polished user experience without feeling foreign to the platform they run on.

The desired result is premium yet native:

- Premium through clarity, restraint, consistency, and attention to detail
- Native through familiar platform patterns, expected behaviour, and compatibility with the surrounding product

Apply this standard whenever work changes a user-facing or administrator-facing interface, interaction, message, workflow, or visual asset.

---

## Core Principle

Make the interface feel like the best version of the platform, not a separate design system forced into it.

Premium does not mean decorative complexity. It means that the experience is coherent, deliberate, trustworthy, accessible, responsive, and complete.

Native does not mean visually generic. Brand character should appear through controlled choices that preserve familiar interaction patterns.

---

## Order of Decision Making

When making a branding or UX decision, use this order:

1. The current task specification
2. The project's approved brand system or design specification
3. The host platform's established interaction and accessibility patterns
4. This branding and UX standard
5. Existing project patterns

Do not invent brand rules when approved assets, tokens, components, or patterns already exist.

---

## Brand Foundations

Before implementing visual changes, inspect the project for:

- Approved logos and marks
- Brand colours and design tokens
- Typography rules
- Iconography
- Existing components and layout conventions
- Voice and terminology
- Accessibility requirements

Reuse approved assets and tokens. Do not redraw logos, substitute fonts, introduce new colours, or alter brand marks without explicit approval.

If no complete brand system exists, derive the smallest consistent set of decisions from the current product. Do not create a broad rebrand to solve a local interface task.

---

## Branding Modes

Every user-facing project or feature must use one of two branding modes:

1. Author Branded
2. Extension Branded

The project specification should declare the mode. If it does not, infer it from where the interface lives and what users understand themselves to be using. Ask only when the distinction would materially change the result.

Branding mode controls the interface treatment. Every product remains authored, owned, maintained, and supported by the declared AlphaSys or Techn brand. The mode does not change the plugin author, code namespace, licence, repository ownership, or required attribution.

### Author Branded

Use Author Branded mode when the interface is primarily an AlphaSys or Techn product experience.

Examples include:

- A standalone Techn plugin settings area
- An AlphaSys client portal
- A product-owned dashboard or workflow
- A public-facing feature whose identity is AlphaSys or Techn

In this mode:

- Use the declared author brand: AlphaSys or Techn.
- Use the shared AlphaSys and Techn navy-and-orange product palette defined in this standard. The shared palette does not merge the two brands: product naming, authorship, metadata, and support ownership must still identify the correct author.
- Use approved author colours, typography, logo treatment, voice, and design tokens.
- Keep platform-native structure and behaviour beneath the branded presentation.
- Use brand emphasis selectively so operational interfaces remain clear and familiar.
- Do not introduce a second visual identity unless the specification requires co-branding.

Author Branded does not permit replacing standard platform behaviour with custom interaction merely for visual distinction.

### Extension Branded

Use Extension Branded mode when an AlphaSys or Techn product extends another product and users should experience the feature as a natural part of the product being extended.

Examples include:

- A Gravity Forms field, feed, settings panel, or entry action
- An extension for WooCommerce or another established WordPress product
- A feature embedded inside a third-party application's existing workflow

In this mode:

- Follow the extended product's current layout, component, spacing, terminology, icon, interaction, and feedback patterns.
- Reuse the product's supported APIs, components, CSS classes, design tokens, and extension points where appropriate.
- Place actions and settings where users of that product expect them.
- Match the product's visual density and hierarchy before adding custom styling.
- Preserve the declared AlphaSys or Techn authorship and ownership throughout the product.
- Keep visible author branding discreet and subordinate to the extended product's interface, using it where appropriate for authorship, support, documentation, settings metadata, and repository information.
- Do not add prominent author-brand colours, logos, banners, or application shells that make the extension feel separate from the product.
- Do not copy private assets or unsupported implementation details solely to imitate the product.
- Do not imply that the extension is officially produced, endorsed, or owned by the extended product unless that claim is authorised.
- Use third-party names, logos, and trademarks only where permitted and necessary to identify compatibility.

Extension Branded means an AlphaSys or Techn product that is visually and behaviourally compatible with what it extends. It does not mean hiding ownership, surrendering product identity, or presenting the extension as deceptively official.

### Choosing the Mode

Use Author Branded when the user is primarily interacting with an AlphaSys or Techn product.

Use Extension Branded when the user is primarily interacting with the product being extended and the new feature sits inside that product's interface or workflow.

For mixed experiences, choose a primary mode for each surface rather than blending both brands everywhere. For example, an embedded Gravity Forms settings panel may be Extension Branded while a separate Techn account portal remains Author Branded.

Document the selected mode in the implementation notes and apply it consistently across the relevant surface.

---

## Brand Mode Implementation

The branding mode determines where visual tokens come from. It must be decided before interface styling begins.

| Decision | Author Branded | Extension Branded |
|---|---|---|
| Primary visual source | The shared AlphaSys and Techn navy-and-orange system | The core product being extended |
| Structure and behaviour | Native platform patterns | Core product patterns first, then native platform patterns |
| Author identity | Visible and appropriately prominent | Accurate but visually subordinate |
| Hero or branded shell | Permitted for an owned top-level surface | Normally not permitted inside the core product's interface |
| Primary action styling | Author navy with white text | Core product's supported primary action treatment |
| Orange accent | Used selectively | Do not introduce unless it belongs to the core product's palette |
| Fallback | Native platform styling | Native platform styling when the core product provides no supported system |

Do not blend the two modes into a third visual language. In particular, do not place an author-branded navy-and-orange shell around an interface that users understand as part of another plugin.

### Author Brand Tokens

Author Branded products should start with the following semantic tokens. Projects may add tokens, but should not replace these values without an approved design reason.

```css
:root {
	--brand-ink: #0a263b;
	--brand-muted: #5c6d79;
	--brand-orange: #ff6b00;
	--brand-orange-dark: #d95b00;
	--brand-navy: #002840;
	--brand-navy-deep: #001d31;
	--brand-navy-light: #063b5d;
	--brand-navy-highlight: #125c8a;
	--brand-surface: #ffffff;
	--brand-surface-soft: #f2f6f8;
	--brand-border: #d5dfe5;
	--brand-orange-soft: #fff0e5;
	--brand-danger: #b32d2e;

	--space-1: 0.25rem;
	--space-2: 0.5rem;
	--space-3: 0.75rem;
	--space-4: 1rem;
	--space-5: 1.25rem;
	--space-6: 1.5rem;
	--space-8: 2rem;

	--radius-control: 0.4rem;
	--radius-panel: 0.75rem;
	--radius-hero: 0.875rem;
	--shadow-panel: 0 0.2rem 0.8rem rgba(0, 40, 64, 0.07);
	--shadow-hero: 0 0.75rem 2rem rgba(0, 40, 64, 0.2);
}
```

Use tokens by purpose rather than by colour name in component-level CSS where practical. This makes an extension surface easier to retheme without rebuilding its layout.

The core colour roles are:

- Navy communicates authority and anchors the page, headings, and primary actions.
- Orange provides energy and emphasis for short accents, selected details, and focus treatment.
- White and the soft neutral surface provide breathing room around operational content.
- Muted blue-grey supports secondary text without weakening readability.
- WordPress or core-product semantic colours continue to communicate success, warning, error, and destructive actions.

Orange is an accent, not a general-purpose text colour. Verify contrast for its exact size, weight, and background before using it for text. Do not use colour as the only indication of status or required input.

### Author Brand Hero

An owned, top-level product screen may use one compact branded hero to establish identity. It should contain an eyebrow, one page title, a concise value statement, and only useful capability badges.

The preferred background is a tonal navy treatment with a brighter navy highlight:

```css
background:
	radial-gradient(circle at 88% 10%, rgba(18, 92, 138, 0.58), transparent 24rem),
	linear-gradient(125deg, #001d31, #063b5d);
```

Use white for the hero title and lead text, and orange sparingly for the eyebrow or a small accent. The highlight must remain navy; do not use a white glare or decorative orange wash. Keep the hero compact enough that the primary task remains visible near the top of the viewport.

Capability badges must describe capabilities that apply to the current site or context. For example, show a WPML badge only when WPML is active on that site. Do not advertise an integration by rendering inactive or empty interface chrome.

---

## Premium WordPress Admin Pattern

Author Branded WordPress screens should use a consistent composition while retaining WordPress behaviour.

### Screen Order

Use this order for a top-level settings screen:

1. A screen-reader-accessible WordPress page heading as a direct child of `.wrap`
2. WordPress core and third-party admin notices
3. The compact branded hero
4. A migration, setup, or status panel when it is actionable or informative
5. The primary working area
6. A compact developer reference when the product exposes public functions, hooks, or APIs

The direct-child page heading is important because WordPress and third-party plugins use it as an anchor when positioning notices. It may be visually hidden when the branded hero repeats the title, but it must remain accessible. Never capture unrelated admin notices inside the hero, card, or branded background.

Use standard WordPress notice markup and placement for product messages. A save confirmation belongs with the other notices above the branded content, not inside the hero.

### Panels and Hierarchy

Use a white panel on the WordPress admin background, a subtle cool border, a restrained shadow, and approximately `0.75rem` corner radius. A panel should correspond to a real task or information group, not merely decorate the page.

A strong panel hierarchy normally contains:

- A short uppercase eyebrow for context
- A clear heading that names the task
- One concise supporting sentence
- The controls or data needed to complete the task
- A footer only when it contains meaningful actions or status

Keep repeated panels visually quieter than the hero. Avoid placing every small field, statistic, or sentence in its own card.

### Actions

Use one visually dominant primary action per task area:

- Primary: navy background, white text, and a clearly visible hover and focus state
- Secondary: white or neutral surface with a navy or context-appropriate border
- Accent: orange only when it does not compete with the primary action
- Destructive: the platform's recognised red treatment, accompanied by an explicit label

Buttons with icons must use `inline-flex`, align items centrally, and use a consistent gap. Give SVG icons a fixed width and height, a stable `viewBox`, and `currentColor`. Check optical alignment rather than assuming the icon's bounding box is visually centred.

Do not use an icon alone when the action's meaning is not universal. Icon-only controls require an accessible name and visible tooltip or equivalent supporting context.

### Editable Data Tables

For repeatable key/value or settings data:

- Retain familiar WordPress table and form behaviour.
- Use stable column widths and allow the value and notes columns enough room for real content.
- Keep rows compact without making targets difficult to select.
- Mark required fields visibly and programmatically.
- Preserve user-entered order unless sorting is an explicit feature.
- Use a labelled remove action and make destructive intent clear.
- Provide horizontal scrolling or a deliberate stacked layout at narrow widths rather than clipping controls.
- Keep add and save actions distinct: adding a row changes the editor; saving commits the data.
- Show counts in language-neutral terms unless a language integration is active.

### Migration and Status Panels

A migration panel should make transition risk visible. Where data is moved from a legacy implementation, show the source, source count, destination count, language or site scope when applicable, comparison status, and a clear next action.

Use plain states such as:

- Source detected; migration available
- Migration complete; values match
- Destination differs from source; re-import available
- No source detected; the product is ready to operate independently
- Migration failed; data remains unchanged and recovery instructions are available

Informational migration panels may be dismissed. Dismissal should persist per user and per site, and the panel should return when the underlying migration state materially changes. Never dismiss a current error or unresolved destructive warning permanently.

### Conditional Integration UI

An optional integration must not leave visible placeholders when it is absent. Only render integration-specific badges, metrics, filters, help text, and terminology after runtime detection confirms that the integration is active for the current site.

For a language integration such as WPML:

- Show the active language context, language counts, and fallback-language explanation only when WPML is active.
- On multisite, detect the integration in the current site's context rather than assuming network activation means every site uses it.
- Do not display `0 languages`, `in this language`, or `WPML-ready` as operational status when WPML is absent.
- Preserve each language independently during migration and confirm counts for every language.

Apply the same principle to commerce, forms, caching, analytics, and other optional dependencies.

### Developer Reference

When public functions or APIs are part of the product, keep the reference visible but compact. A good desktop composition places the description at the left, a small test control in the middle, and copyable examples at the right. The row should remain narrow and vertically centred, then stack cleanly when space becomes limited.

A built-in lookup tester should:

- Use the product's native API directly rather than a global function that a legacy plugin may own.
- Require the same capability as the settings screen.
- Use a nonce for asynchronous requests.
- Sanitize the submitted key and escape returned content.
- Disable or mark the control while a request is running.
- Return a useful not-found or error message.
- Prefer inline feedback; use a JavaScript alert only when the product specification explicitly calls for it.

Code samples, labels, menu names, headings, and plugin metadata must use the same product name and terminology.

---

## Compatibility During Product Transitions

Premium UX includes safe coexistence with the implementation being replaced.

When a new plugin is intended to run beside a legacy plugin:

- Test both plugin load orders.
- Never redeclare an existing global function, class, or constant without a guard.
- Do not assume the new plugin will load first or that activation order controls runtime order.
- Separate the new plugin's internal API from compatibility wrappers.
- Allow the legacy public function to continue operating while the legacy plugin owns it.
- Import from the legacy source without requiring the legacy plugin to be disabled first.
- Keep source data unchanged until migration is verified.
- Provide visible source and destination counts so the user can compare before deactivation.
- Make re-import explicit and explain whether it merges or replaces destination data.
- On network activation, process each site in its own context, restore the original site context, and report per-site failures.
- On multisite, account for sites created after network activation.
- For multilingual data, migrate every available language rather than only the currently selected admin language.

Compatibility shims should be narrow, documented, and removable. Broad aliases can conceal ownership errors and make failures harder to diagnose.

---

## Premium Quality Principles

Premium interfaces should demonstrate:

- Clear visual hierarchy
- Consistent spacing and alignment
- Strong typography and readable line lengths
- Restrained use of colour and emphasis
- Purposeful grouping and progressive disclosure
- High-quality empty, loading, success, warning, and error states
- Predictable interactions and immediate feedback
- Careful responsive behaviour
- Consistent component states
- Polished details that support the task rather than distract from it

Prefer one strong visual idea over several competing effects.

Avoid using decoration to compensate for weak hierarchy or unclear content.

---

## Native Experience Principles

Use the host platform's established patterns for:

- Navigation
- Page structure
- Forms and validation
- Buttons and links
- Dialogues and confirmations
- Notifications and status messages
- Tables, lists, filters, and pagination
- Permissions and destructive actions
- Keyboard interaction
- Loading and progress feedback

Controls must look and behave like their purpose. Do not make links resemble buttons, replace standard controls without a functional reason, or create unfamiliar interaction patterns for routine tasks.

Use platform-native components and APIs where they provide the required experience. Custom components are appropriate when they add real product value and still behave consistently with the platform.

---

## WordPress Experience

In WordPress admin interfaces:

- Follow established WordPress information architecture and screen conventions.
- Use WordPress APIs, components, notices, tables, settings patterns, and capability controls where appropriate.
- Keep navigation and actions where WordPress administrators expect to find them.
- Use native update, media, editor, settings, and confirmation flows rather than recreating them.
- Make custom branding subtle enough that the interface still feels part of WordPress admin.
- Do not build a visually isolated application shell inside WordPress unless the specification genuinely requires one.
- Do not remove or disguise familiar WordPress behaviour merely to make the interface look different.
- When using Extension Branded mode, follow the extended plugin's supported admin patterns within the surrounding WordPress conventions.

On the public-facing site, follow the active site's design system and front-end conventions rather than WordPress admin styling.

---

## Visual Hierarchy and Layout

Every screen should make the following clear without explanation:

1. Where the user is
2. What the screen is for
3. What requires attention
4. What the primary action is
5. What happens next

Use spacing, typography, alignment, and grouping before adding borders, shadows, backgrounds, or colour.

### Rules

- Keep page structure simple and scannable.
- Use a consistent content width and alignment grid.
- Keep related controls together.
- Separate distinct tasks clearly.
- Maintain comfortable density without wasting space.
- Preserve usable layouts at supported viewport sizes and browser zoom levels.
- Avoid excessive cards, nested panels, and containers within containers.

---

## Colour, Typography, and Effects

Colour must communicate hierarchy or state, not act as decoration alone.

### Rules

- Use approved brand colours and semantic state colours consistently.
- Maintain accessible colour contrast.
- Do not rely on colour alone to communicate meaning.
- Use a restrained type scale with clear heading levels.
- Prefer the platform or project typography unless the brand system specifies otherwise.
- Keep shadows, gradients, blur, glass effects, and animation subtle and purposeful.
- Avoid fashionable visual effects that conflict with the host platform or reduce readability.
- Do not introduce dark mode unless the project supports it comprehensively or the task requires it.

Follow the project's CSS unit and architecture standards for all implementation values.

---

## Components and Interaction States

Components must be consistent across all states.

For each interactive component, consider:

- Default
- Hover
- Focus
- Active or selected
- Disabled
- Loading
- Success
- Warning
- Error
- Empty or unavailable

### Rules

- Provide visible keyboard focus.
- Keep hit targets comfortably usable.
- Prevent duplicate submissions where appropriate.
- Show progress for operations that are not immediate.
- Preserve user input after recoverable errors.
- Place validation feedback close to the affected field and provide a useful summary when needed.
- Confirm destructive or difficult-to-reverse actions.
- Do not use animation as the only indication of state.

---

## Content and Voice

Interface copy is part of the user experience.

### Rules

- Use the project's established terminology.
- Write concise, specific labels and instructions.
- Use action-led button labels such as `Save settings` instead of vague labels such as `Submit`.
- Explain errors in plain language and provide a recovery path.
- Avoid internal implementation language, blame, hype, and unnecessary jargon.
- Keep capitalisation, punctuation, dates, numbers, and terminology consistent.
- Do not claim success before an operation has completed.

The tone should feel calm, capable, and trustworthy.

---

## Accessibility

Accessibility is a quality requirement, not a visual compromise.

### Minimum Requirements

- Use semantic HTML and correct control types.
- Provide programmatic labels and instructions.
- Support keyboard-only operation.
- Maintain visible focus states.
- Use logical heading structure and focus order.
- Provide sufficient colour contrast.
- Announce dynamic status and error changes where appropriate.
- Respect reduced-motion preferences.
- Ensure content remains usable when zoomed and reflowed.
- Provide text alternatives for meaningful images and icons.

Do not ship a visual treatment that reduces accessibility compared with the native platform component it replaces.

---

## Responsive Behaviour

Responsive design must preserve task completion, not merely prevent horizontal scrolling.

### Rules

- Start with the smallest supported layout.
- Prioritise content and actions at narrow widths.
- Allow controls and text to wrap naturally.
- Avoid fixed heights where content may grow.
- Keep tables usable through appropriate reflow, scrolling, or alternate presentation.
- Test long labels, validation messages, and realistic content.
- Do not hide essential actions solely to make a layout fit.

---

## Motion and Feedback

Motion should explain change, preserve context, or provide feedback.

### Rules

- Keep motion brief and subtle.
- Avoid decorative animation in task-focused interfaces.
- Respect `prefers-reduced-motion`.
- Never delay an action so an animation can finish.
- Provide immediate acknowledgement after user input.
- Use skeletons, spinners, or progress indicators only when they clarify actual loading state.

---

## UX Completeness

Do not implement only the ideal path.

Every workflow should account for:

- First use
- Empty data
- Loading
- Success
- Partial completion
- Invalid input
- Permission restrictions
- Network or service failure
- Retry or recovery
- Destructive actions
- Completion and the next useful action

Preserve existing behaviour unless the task explicitly changes it.

---

## Anti-Patterns

Avoid:

- Generic dashboard templates that ignore the host platform
- Excessive cards, pills, gradients, shadows, or rounded containers
- Large decorative hero sections in operational screens
- Multiple competing primary actions
- Icon-only controls without an accessible name or clear meaning
- Placeholder copy, fake data, or unfinished states in production work
- Custom controls that are less usable than native equivalents
- Layout shifts caused by loading or validation
- Toast-only errors that disappear before they can be understood
- Branding that overpowers the user's task
- Broad visual redesigns when the requested change is local

---

## Validation and Handoff

Before finalising user-facing work, verify:

- The interface follows approved brand assets and project conventions.
- The selected branding mode is documented and consistently applied.
- Extension Branded work feels native to the extended product without implying unauthorised endorsement.
- The result feels consistent with the host platform.
- Primary tasks and actions are immediately clear.
- Keyboard, focus, labels, contrast, and status feedback are usable.
- Loading, empty, success, and error states are present where relevant.
- Responsive behaviour works at representative viewport sizes and zoom levels.
- Existing workflows and permissions remain intact.
- Visual effects are restrained and purposeful.
- No placeholder content or unfinished interaction remains.

For WordPress admin work, also verify in a real WordPress runtime:

- Core, product, and third-party notices render above the branded interface and never inside the hero.
- The screen works with each optional integration both active and inactive.
- Integration-specific badges, counts, and language or product terminology disappear completely when they do not apply.
- Empty, populated, matched, changed, no-source, failure, and dismissed migration states behave as specified.
- A dismissed informational panel returns when its underlying status changes.
- Icons and labels are optically aligned at normal and increased browser zoom.
- Tables, developer tools, actions, and long labels remain usable at desktop and narrow widths.
- Async actions handle permissions, nonce failure, loading, success, not-found, and server error states.
- Destructive actions are labelled, protected, and recoverable where practical.
- Screenshots have been reviewed for spacing, alignment, density, hierarchy, clipping, and unintended notice placement.
- When replacing a legacy product, both plugin load orders and the intended coexistence-to-deactivation workflow have been tested.
- On multisite or multilingual products, representative sites and languages have been compared individually rather than inferred from a network total.

The final response must state:

- Whether Author Branded or Extension Branded mode was used
- Which branding and UX standards were applied
- What interface states and viewport sizes were checked
- What accessibility validation was performed
- Any known visual, platform, or testing limitations

---

## Default Rule

When uncertain, choose the solution that feels familiar on first use and refined through repeated use.

The interface should express the brand without making the user relearn the platform.
