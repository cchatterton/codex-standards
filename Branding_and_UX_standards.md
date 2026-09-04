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

Branding mode controls the interface treatment. It does not change the plugin author, code namespace, licence, repository ownership, or required attribution.

### Author Branded

Use Author Branded mode when the interface is primarily an AlphaSys or Techn product experience.

Examples include:

- A standalone Techn plugin settings area
- An AlphaSys client portal
- A product-owned dashboard or workflow
- A public-facing feature whose identity is AlphaSys or Techn

In this mode:

- Use the declared author brand: AlphaSys or Techn.
- Use approved author colours, typography, logo treatment, voice, and design tokens.
- Keep platform-native structure and behaviour beneath the branded presentation.
- Use brand emphasis selectively so operational interfaces remain clear and familiar.
- Do not introduce a second visual identity unless the specification requires co-branding.

Author Branded does not permit replacing standard platform behaviour with custom interaction merely for visual distinction.

### Extension Branded

Use Extension Branded mode when the work extends an existing product and users should experience the feature as a natural part of that product.

Examples include:

- A Gravity Forms field, feed, settings panel, or entry action
- An extension for WooCommerce or another established WordPress product
- A feature embedded inside a third-party application's existing workflow

In this mode:

- Follow the extended product's current layout, component, spacing, terminology, icon, interaction, and feedback patterns.
- Reuse the product's supported APIs, components, CSS classes, design tokens, and extension points where appropriate.
- Place actions and settings where users of that product expect them.
- Match the product's visual density and hierarchy before adding custom styling.
- Keep AlphaSys or Techn identity to appropriate authorship, support, documentation, and repository metadata unless visible co-branding is explicitly required.
- Do not add prominent author-brand colours, logos, banners, or application shells that make the extension feel separate from the product.
- Do not copy private assets or unsupported implementation details solely to imitate the product.
- Do not imply that the extension is officially produced, endorsed, or owned by the extended product unless that claim is authorised.
- Use third-party names, logos, and trademarks only where permitted and necessary to identify compatibility.

Extension Branded means visually and behaviourally compatible, not deceptively official.

### Choosing the Mode

Use Author Branded when the user is primarily interacting with an AlphaSys or Techn product.

Use Extension Branded when the user is primarily interacting with the product being extended and the new feature sits inside that product's interface or workflow.

For mixed experiences, choose a primary mode for each surface rather than blending both brands everywhere. For example, an embedded Gravity Forms settings panel may be Extension Branded while a separate Techn account portal remains Author Branded.

Document the selected mode in the implementation notes and apply it consistently across the relevant surface.

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
