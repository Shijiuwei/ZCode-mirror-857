# ZCode Design System

Portable design system for AI-assisted UI work in this repository.

This file is meant for coding agents. When generating or editing UI in this repo, follow this file before inventing new visual rules.

## Highest-priority UI constraint

The dedicated `text-ui-*` scale is a mandatory repository-wide constraint for application interface typography:

- UI components must use `text-ui-xl`, `text-ui-lg`, `text-ui-base`, `text-ui-caption`, `text-ui-sm`, or `text-ui-xs`.
- Do not introduce Tailwind's built-in `text-base`, `text-sm`, or `text-xs` for application UI.
- Do not introduce arbitrary UI font sizes such as `text-[13px]` or inline `font-size` values.
- The only content-level exceptions are code, Diff, and terminal rendering that consume their independent numeric font-size settings. Their surrounding controls, labels, headers, and metadata must still use `text-ui-*`.
- Mobile Web editable inputs that must prevent iOS focus zoom use the fixed `text-mobile-input-safe` compatibility token (16px). Do not use it as a general UI hierarchy token.
- Never implement interface font scaling by changing `html` or `document.documentElement.style.fontSize`; update only `--ui-font-size`.

Treat violations of this section as design-system defects, not stylistic preferences.

## Product Character

ZCode is a desktop-first and web-compatible AI workspace. The interface should feel calm, dense, and operational rather than decorative.

Design for:

- long sessions
- high information density
- readable chat and tool output
- keyboard-driven workflows
- desktop and web parity
- macOS, Windows, and Linux compatibility
- internationalization and variable text length
- light and dark themes, plus Zai variants already supported by the codebase

Avoid:

- oversized marketing-style spacing
- playful gradients as the default UI language
- bright full-surface brand fills
- ambiguous hierarchy between background, card, and popover surfaces

## Theme Modes

User-facing theme choices are:

- System
- Light Theme, backed by Zai Light
- Dark Theme, backed by Zai Dark

Default light and dark CSS variables still exist as fallback foundations, but new UI should be validated against Zai Light and Zai Dark as the active light/dark experiences.

## Color Palette

### Core semantic colors

- **Brand**: `--color-brand`
  Use for key emphasis, important links, active indicators, and brand-accented actions. Never use as a full-page background.
- **Icon Blue**: `--color-icon-blue`
  Use for browser-style links and blue icon emphasis that follows the Figma `icon/blue` role. Keep file-type icons on their own descriptor colors.
- **Accent Surface**: `--color-accent`
  Use for weak emphasis blocks, selected highlights, and low-intensity branded surfaces.
- **Background**: `--color-background`
  Default page and app workspace background.
- **Background Alt**: `--color-background-alt`
  Alternate page region background when the layout needs a soft separation.
- **Header / Panel / Sidebar**: `--color-header`, `--color-panel`, `--color-sidebar`
  Structural layout surfaces only. Do not reuse as generic card colors.
- **Surface**: `--color-surface`
  Low-elevation container surface.
- **Surface Hover**: `--color-surface-hover`
  Hover state for low-elevation surfaces.
- **Card**: `--color-card`
  Standard content card background.
- **Card Selected**: `--color-card-selected`
  Selected or active card background.
- **Popover**: `--color-popover`
  Dialog, popover, and floating panel background.
- **Menu**: `--color-menu`
  Dropdown and menu surface.
- **Menu Hover**: `--color-menu-hover`
  Hover state for menu items.
- **Input**: `--color-input`
  Default editable field background.
- **Input Focused**: `--color-input-focused`
  Focused editable field background.

### Text colors

- **Text Primary**: `--color-foreground`
  Main reading text.
- **Text Secondary**: `--color-foreground-subtle`
  Metadata, descriptions, supporting labels.
- **Text Tertiary**: `--color-foreground-subtlest`
  Placeholders, weak hints, disabled-adjacent copy.
- **Text Inverse**: `--color-foreground-inverse`
  Text on dark, branded, or state-colored fills.

### Border colors

- **Border**: `--color-border`
  Default border and separator.
- **Border Hover**: `--color-border-hover`
  Hovered border state.
- **Card Border**: `--color-card-border`
- **Popover Border**: `--color-popover-border`
- **Input Border**: `--color-input-border`
- **Input Border Hover**: `--color-input-border-hover`
- **Input Border Focused**: `--color-input-border-focused`

### Semantic feedback colors

- **Success**: `--color-success` with `--color-success-foreground`
- **Warning**: `--color-warning` with `--color-warning-foreground`
- **Destructive**: `--color-destructive` with `--color-destructive-foreground`
- **Idle-time task**: `--color-idle-task` with `--color-idle-task-surface`
  Use only for idle-time queue and paused tags so they remain visually distinct
  from the green scheduled-task treatment.
- **Diff Added**: `--color-diff-added` with `--color-diff-added-foreground`
- **Diff Removed**: `--color-diff-removed` with `--color-diff-removed-foreground`

Use semantic colors only for actual semantic states. Do not borrow success, warning, or destructive colors just to make a block feel louder.

### Blocking interaction colors

- **Ask Interaction**: `--color-interaction-ask-surface`, `--color-interaction-ask-foreground`, `--color-interaction-ask-fill`
  Legacy compatibility tokens for `AskUserQuestion`; waiting badges no longer use a separate blue treatment.
- **Confirmation Interaction**: `--color-interaction-confirmation-surface`, `--color-interaction-confirmation-foreground`
  Use for all waiting badges, including `AskUserQuestion`, permission, and `ExitPlanMode`.

Waiting badges use one green confirmation treatment so identical waiting copy does not appear as different states. Do not substitute `--color-success` for a waiting confirmation.

### Workflow timeline colors

The dynamic-workflow timeline draws with a feature-scoped token family:

- **Rule**: `--color-workflow-rule`
  Sub-hairline for repeated furniture. Weaker than `--color-border`; do not use it as a
  general separator.
- **Trace / Trace Strong**: `--color-workflow-trace`, `--color-workflow-trace-strong`
  Rail and arc stroke ramp: not yet taken vs control has passed. The marching segment
  overlays `--color-warning` dashes; there is no third stroke colour.
- Station lamps and agent pills use the semantic status colours (`--color-success`,
  `--color-warning` for running, `--color-destructive`); agent avatars use the fixed nine-color HEX palette,
  assigned by instance index (name hash fallback), and never encode status.
- A compile-feedback row (a script that did not compile, so nothing ran) uses a hollow lamp:
  `--color-warning` while it is the latest draft, `--color-foreground-subtlest` once a newer
  draft exists. Never `--color-destructive`, which on this feature belongs to a run that errored.

### Overlay and utility colors

- **Toast**: `--color-toast`
- **Tooltip**: `--color-tooltip`
- **Tooltip Text**: `--color-tooltip-foreground`
- **Tooltip Tag**: `--color-tooltip-tag`
- **Tooltip Tag Text**: `--color-tooltip-tag-foreground`
- **Tag**: `--color-tag`
- **Find Highlight**: `--color-find-highlight`, `--color-find-highlight-active`
  Use only for in-page/text search result highlights. The base token marks all matches; the active token marks the currently selected match.
- **Hover**: `--color-hover`
- **Selected**: `--color-selected`
- **Primary**: `--color-primary`
- **Primary Foreground**: `--color-primary-foreground`
- **Secondary**: `--color-secondary`

## Color Usage Rules

- Use semantic tokens, not raw one-off color values.
- Prefer `bg-background + text-foreground` for page roots.
- Prefer `bg-card` or `bg-surface` for normal content containers.
- Prefer `bg-popover` or `bg-menu` for overlays; pair with `border-popover-border` or `border-border`.
- Prefer `bg-hover` for generic hover and `bg-selected` for selection.
- Prefer `bg-primary text-primary-foreground` for the default primary button.
- Prefer text hierarchy to create information density before adding more borders or colors.
- Keep brand color usage sparse and intentional.
- Never mix `bg-background`, `bg-card`, and `bg-surface` without a clear layering reason.
- Never replace semantic tokens with ad hoc values like `text-white/60`, `border-white/10`, or arbitrary neutral alpha fills.

- `DesktopWindowFrame` uses `bg-background-alt` on macOS desktop. Windows, Linux, and Web keep `bg-background-win-alt`; child surface colors remain independent.
- Linux desktop uses a `16px` outer window-shell radius around the `12px` workspace panels and their `4px` outer inset. Keep the shell radius and compositor clip path equal; maximized windows use `0px`.

## Typography

### Font families

- **UI Sans**: use the app's default `font-sans` stack for almost all interface text.
- **UI Mono**: use `font-mono` for paths, commands, code, identifiers, shortcuts, commit hashes, model IDs, and terminal-like data.

### UI font tokens

All interface typography must use the dedicated `text-ui-*` scale. The Appearance setting controls `--ui-font-size`, whose default is `14px`:

| Token             | Formula                | Default |
| ----------------- | ---------------------- | ------: |
| `text-ui-xl`      | `--ui-font-size + 4px` |    18px |
| `text-ui-lg`      | `--ui-font-size + 2px` |    16px |
| `text-ui-base`    | `--ui-font-size`       |    14px |
| `text-ui-caption` | `--ui-font-size - 1px` |    13px |
| `text-ui-sm`      | `--ui-font-size - 2px` |    12px |
| `text-ui-xs`      | `--ui-font-size - 4px` |    10px |
| `text-ui-2xs`     | `--ui-font-size - 5px` |     9px |

`text-ui-2xs` is a restricted exception below the `text-ui-xs` floor: it is permitted
only for graph **axis furniture** — timebase tick labels, channel codes, and unit
suffixes in the workflow timeline — never for content, labels, or metadata.
Treat any other use as a design-system defect.

`text-mobile-input-safe` is a fixed 16px platform-compatibility token. It is
reserved for editable controls on mobile Web surfaces where iOS focus zoom must
be prevented, and therefore does not scale with `--ui-font-size`.

- Changing the interface font size updates only `--ui-font-size`; never mutate the root `html` font size.
- Icons, spacing, radii, and other `rem`-based geometry must not scale with the interface font setting.
- Code, Diff, and terminal content retain their independent font-size settings; only their surrounding interface controls use `text-ui-*`.
- Mobile Web inputs that require the iOS 16px focus-zoom floor use `text-mobile-input-safe`.

### Type roles

The `text-ui-*` scale expresses stable semantic roles. Choose a token by content role rather than by isolated visual preference:

| Token             | Primary roles                                                                                                |
| ----------------- | ------------------------------------------------------------------------------------------------------------ |
| `text-ui-xl`      | Markdown `h1` and equivalent first-level reading headings                                                    |
| `text-ui-lg`      | Markdown `h2` and equivalent second-level reading headings                                                   |
| `text-ui-base`    | Markdown `h3`-`h6`, body copy, common buttons, workspace and section titles, and other primary UI text       |
| `text-ui-caption` | Compact supporting copy that must remain one step below body text, such as the New Task feature announcement |
| `text-ui-sm`      | Secondary copy, supporting information, helper text, Tooltip copy, and Markdown inline code                  |
| `text-ui-xs`      | Tooltip keyboard shortcuts, badges, compact labels, counters, and very weak metadata                         |

- Markdown `h3`-`h6` share `text-ui-base`; distinguish their hierarchy through weight: `h3`-`h4` use `font-semibold`, `h5` uses `font-medium`, and `h6` uses `font-normal`.
- Body copy and common controls normally use `text-ui-base font-normal`; titles and labels may strengthen weight without changing their semantic size role.
- Pair secondary copy with an appropriate semantic color, usually `text-foreground-subtle`. Pair weak metadata with `text-foreground-subtlest`. Font size and color hierarchy are independent decisions.
- **Tooltip**: titles, plain descriptions, and log content use `text-ui-sm`; keyboard shortcut labels use `text-ui-xs`. Rich content such as Markdown release notes keeps its content typography hierarchy instead of flattening headings and links to the Tooltip copy size.
- **Code / Command / Path**: use `font-mono`, usually with `text-ui-base`; Markdown inline code uses `text-ui-sm`.
- When migrating a legacy explicit `13px` UI size without a deliberate compact-caption role, use `text-ui-base`. Use `text-ui-caption` only where the product explicitly requires a stable one-step-below-body caption treatment. Do not collapse either into `text-ui-xs`; `text-ui-xs` is reserved for badge-scale and very weak metadata roles.

### Markdown type scale

Markdown rendered through the shared assistant response uses a reading-oriented hierarchy while the surrounding operational UI remains compact:

- **User and assistant message containers**: `text-ui-base`
- **Body**: `text-ui-base`
- **Links**: `text-ui-base`
- **Inline code**: `font-mono text-ui-sm`
- **Code block body**: default `14px`, configurable through code preview settings
- **Code block header**: `text-ui-base`
- **Tables**: `text-ui-base`
- **h1**: `text-ui-xl`
- **h2**: `text-ui-lg`
- **h3**: `text-ui-base`
- **h4**: `text-ui-base`
- **h5**: `text-ui-base`
- **h6**: `text-ui-base`

### Typography rules

- Keep UI text compact and readable.
- Prefer `font-medium` for headings and labels; avoid heavy weights unless there is a strong reason.
- Use monospace only where the content is inherently technical.
- Use `text-ui-base` as the default compact app text size. Keep `text-ui-base` for workspace titles and stronger title treatments.
- Respect i18n expansion. Do not hard-code layouts that only work for short English labels.
- Do not depend on tight truncation as the only way a component survives translation.

## Spacing

Base spacing unit is `4px`.

Recommended rhythm:

- `4px`: tight icon/text spacing
- `8px`: compact control padding and inline gaps
- `12px`: dense list items and menu rows
- `16px`: standard card and panel padding
- `20px` to `24px`: larger sections or dialog interiors

Spacing rules:

- Keep dense operational UI compact by default.
- Prefer a small set of repeated gaps and paddings instead of arbitrary values.
- In flex layouts with text, add `min-w-0` where truncation or shrink is required.
- In nested scroll or split-panel layouts, add `min-h-0` where scrolling must be allowed.
- Prefer `w-full + max-w-*` over hard-coded widths when possible.

## Radius

Radius follows the nesting of visible rounded containers, not component importance or layout depth. These rules are the target specification; existing component defaults may still need migration.

### Container hierarchy

- Layout regions, ordinary wrappers, groups, and separators do not count as radius levels.
- The first rounded container starts at `rounded-xl`, whether inside or outside a layout region.
- Nested rounded containers step down through `rounded-lg` → `rounded-md` → `rounded-sm`; `rounded-sm` is the minimum.
- Count the nearest actual rounded container, not intermediate DOM wrappers. Peer containers at the same level use the same radius.
- Cards, chat bubbles, tool blocks, and ordinary composite input shells follow this hierarchy; size or importance alone does not justify `rounded-2xl`.

### Approved 2xl exceptions

The following explicit exceptions may retain `rounded-2xl`; do not extend them to other components based only on size or importance:

- **Main chat input shell**: the actual `ChatPromptEditor` input shell may use `rounded-2xl`. Its drag overlay matches the shell because it covers the same surface, rather than introducing a nested container.
- **Conversation status floating panel**: the shared shell may retain `rounded-2xl` across collapsed and expanded presentations.
- **Toast**: the independent notification shell may retain `rounded-2xl`.
- **Brand icon backplates**: welcome-screen brand art and plugin-detail icons may retain `rounded-2xl` as part of their icon shape. This is not a general exception for icon buttons or content cards.

The main composer region, including its context-header layout wrapper, is layout and does not count as a radius level. Its existing decorative radius does not force the actual input shell down a level. This classification alone does not prescribe changing the layout wrapper's decorative radius.

These exceptions do not automatically grant `2xl` to nested controls or content containers. Basic controls still use the table below. Dialog shells follow their separate rules.

### Basic controls

Buttons, menu trigger buttons, ordinary Input, Textarea, and Select triggers default to `rounded-lg`. Adjust them according to the nearest rounded parent container:

| Nearest rounded parent          | Control radius |
| ------------------------------- | -------------- |
| None, or `rounded-xl` and above | `rounded-lg`   |
| `rounded-lg`                    | `rounded-md`   |
| `rounded-md` / `rounded-sm`     | `rounded-sm`   |

Control size and primary/secondary action emphasis do not independently change radius.

### Dialogs

- Dialog shells, including alert and confirmation dialogs, use `rounded-2xl`.
- Only chat attachment preview, feedback screenshot preview, and CUA screenshot preview dialogs keep `rounded-xl` shells.
- The dialog shell does not count toward its content hierarchy. The first rounded content container starts again at `rounded-xl`, followed by `rounded-lg` → `rounded-md` → `rounded-sm`.
- Basic controls inside dialogs follow the control table; a layout wrapper does not introduce an extra level.

### Menus and selection overlays

- Dropdown menus, context menus, Select expanded panels, and similar option/action overlays use `rounded-lg` shells. This includes the main input's `@` / `/` suggestion panel; it does not inherit the main-input exception.
- Menu items and option hover/selected backgrounds use `rounded-md`.
- Rounded containers or controls nested inside an option use `rounded-sm`.
- Each submenu is an independent overlay: its shell restarts at `rounded-lg`, and its options use `rounded-md`.
- Overlay radius does not inherit the trigger's parent hierarchy. Triggers themselves follow the basic-control table.
- Other standalone popover containers follow the ordinary container hierarchy, starting at `rounded-xl`.

### Shape exceptions and consistency

- `rounded-full` is reserved exclusively for deliberate pill shapes or circles.
- Buttons, tags, counters, and icon buttons do not qualify for `rounded-full` merely because of their component type.
- Do not introduce arbitrary radius values or use the ambiguous bare `rounded` utility.
- Use `rounded-none` or side-specific radius removal only where joined surfaces must form a continuous shape.
- Apply the same rules on desktop and mobile Web, across platforms and themes.

## Sizing

### Common size baselines

- **Icon sizes**: `size-3`, `size-3.5`, `size-4`, `size-5`, `size-6`
- **Control heights**: `h-6`, `h-7`, `h-8`, `h-9`
- **Square icon buttons**: `size-6`, `size-7`, `size-8`, `size-9`

### Sizing rules

- `size-4` is the default UI icon baseline.
- Reuse existing button sizes instead of creating new height systems.
- Prefer fluid widths for content containers.
- Fixed widths are acceptable for menus, popovers, dialogs, and stable side panels.
- Avoid arbitrary `w-[...]` and `h-[...]` for ordinary business UI.

## Components

### Buttons

Preferred button system matches `packages/ui/src/components/ui/button.tsx`.

- **Primary**: `bg-primary text-primary-foreground`
- **Outline**: border-based, neutral surface, subtle hover
- **Secondary**: `bg-secondary text-foreground`
- **Ghost**: transparent until hover
- **Destructive**: semantic destructive fill
- **Link**: text-only with underline on hover

Preferred sizes:

- `xs`: very compact controls
- `default`: most action buttons
- `sm`: compact buttons in dense lists
- `lg`: higher-emphasis buttons
- `icon-*`: icon-only actions

Button rules:

- Use existing button variants first.
- Keep icon-only buttons square.
- Do not promote every action to primary.
- Preserve a clear action hierarchy within each panel.

### Inputs

- Default to `bg-input border-input-border text-foreground`
- Hover with `border-input-border-hover`
- Focus with `border-input-border-focused` and `bg-input-focused`
- Normal form fields default to `rounded-lg`, stepping down according to the nearest rounded parent in the Radius rules.
- Ordinary composite input shells follow the container hierarchy, starting at `rounded-xl`; the main chat input shell is an approved `rounded-2xl` exception.

Input rules:

- Inputs should feel calm and integrated, not glowing by default.
- Use semantic error state styling only for real validation problems.
- Do not style ordinary inputs like cards.

### Cards and Panels

- Standard cards use `bg-card border-card-border`; radius follows the container hierarchy, starting at `rounded-xl`.
- Low-emphasis containers use `bg-surface`
- Selected cards may use `bg-card-selected`
- Main content cards usually use `16px` horizontal padding and compact text

Card rules:

- Cards should clearly sit above the page background but below overlays.
- Keep card surfaces quieter than overlays.
- Avoid mixing multiple card background styles in the same view unless they encode real hierarchy.

### Menus, Popovers, Dialogs

- Menus use `bg-menu`, compact rows, `rounded-lg`, and `shadow-md`
- Menus use `border border-popover-border` and typically `p-1`
- Dropdown menus, context menus, and select popovers should share the same menu surface language
- Adjacent option rows in dropdown menus, context menus, and select popovers use a fixed `2px` vertical gap (`gap-0.5`) at the shared option-stack layer
- Popovers and dialogs use `bg-popover` and `border-popover-border`. Ordinary standalone popovers start at `rounded-xl`; dialogs use `rounded-2xl` except the three preview exceptions in the Radius rules.
- Toasts use `bg-toast`, compact padding, and stronger shadow

Overlay rules:

- Floating UI should feel precise and compact.
- Interactive overlays such as dropdown menus, context menus, selects, and popovers must render above passive tooltips when both are open. Tooltips must never cover options or controls in an active interactive overlay.
- Dropdown menu surfaces must keep their overlay shadow from the first open frame through keyboard focus and pointer hover. Global focus-reset rules must not clear the menu shadow while the content root owns focus.
- Keep menu rows dense and highly scannable.
- Avoid giant popovers with loose spacing unless the task genuinely needs it.
- Do not style menus like cards or reuse `bg-card` / `bg-surface` for ordinary menu content.
- Menu items should read as compact action rows, not miniature buttons.
- Prefer stable item baselines such as compact height, `rounded-md`, `px-2`, `gap-2`, and `text-ui-base`.
- Use subtle hover states such as `bg-menu-hover`; avoid strong fills or per-row borders for normal menu items.
- Disabled items should keep their layout and hierarchy, and usually only drop to a weaker text color.
- Prefer weak separators and ordering to group actions; avoid building menus as stacked sub-cards.
- Reuse the same action-list content across dropdown and context-menu entry points when the action set is the same.
- For selected items, prefer checkmarks, radio indicators, or trailing state markers over strong full-row selection fills.
- Composite triggers with a primary action and a chevron should use a segmented shell: main action on the left, menu reveal on the right.
- Select triggers should continue to follow input-style semantics even when their expanded surface matches menu styling.
- Standard dropdown menus should usually align to the trigger's leading edge unless the trigger sits on the trailing side of a layout.
- Use end alignment when it helps menus open back toward the main content area or prevents edge crowding.
- Context menus should prioritize pointer context and appear near the interaction point rather than mimicking button anchoring.
- Select popovers should preserve a stable relationship with the trigger width and edges whenever practical.
- Keep menu offset small and consistent so the menu feels attached to its trigger while leaving enough room for border and shadow separation.
- Increase offset only when needed to avoid border collision, shadow merging, or layout crowding.

### Tabs and Selection States

- Default inactive tabs remain neutral
- Active tabs use a stronger surface contrast, not brand-fill blocks
- Use `bg-selected` for selected list rows or tabs when appropriate

### Chat, Tooling, and Developer UI

- Chat bubbles follow the container hierarchy, starting at `rounded-xl`. Main chat input shells may retain `rounded-2xl` under the approved exception.
- Tool output, terminal-like blocks, paths, hashes, and commands should bias toward monospace
- Diff UI must use diff-specific semantic colors, not generic success/destructive colors
- Dense operational panels are preferred over marketing-card styling

## Elevation and Depth

ZCode should use restrained depth. Layer primarily through background contrast, borders, and radius before relying on heavy shadows.

Recommended elevation levels:

- **Base**: no shadow, structure comes from background contrast
- **Surface**: very subtle border-led separation
- **Overlay**: `shadow-md` for menus, popovers, dialogs
- **Attention**: `shadow-lg` only for toast, important floating cards, or rare emphasized panels

Depth rules:

- Do not rely on large soft shadows for ordinary layout.
- Menus and dialogs can feel elevated, but still compact and controlled.
- Background layering is usually more important than shadow strength.

## Motion

- Keep transitions fast and low-drama.
- Favor subtle fade, zoom, and directional slide for overlays.
- Motion should clarify state change, not decorate the screen.
- Avoid long, springy, or playful animations in the main workspace.

## Workspace layout

Desktop and wide Web workspace content uses independent conversation, bottom terminal, and Side Pane frames. The conversation frame contains WorkspaceHeader and conversation; the optional terminal has its own frame below it, and Side Pane owns its tab bar. Frames use their own background and border, with 4px resizable gaps matching the macOS outer inset. Resize handles keep a transparent 4px hit area and show a 2px tertiary foreground (`foreground-subtlest/50`) line on hover, focus or drag. The indicator extends along the panel edge, inset by the panel radius at both ends, with rounded ends and no mask. Layout frames do not count toward content radius levels. Mobile remote control retains its single-column and drawer presentation.

## Responsive Behavior

The product is desktop-first, but UI must remain functional on smaller screens.

Rules:

- Start with a stable base layout, then enhance with breakpoints.
- Use breakpoints mainly for layout, width, visibility, and density changes.
- Do not change the semantic meaning of components across breakpoints.
- Prefer changing `max-width`, `grid`, `flex`, and visibility over changing component identity.
- Preserve primary actions at all breakpoints; do not hide core workflows behind desktop-only affordances.

## Accessibility and Internationalization

- Support keyboard navigation as a first-class interaction path.
- Preserve visible focus behavior through the repo's established focus styling patterns.
- Ensure contrast remains safe in light, dark, and Zai modes.
- Write layouts that tolerate longer translations.
- Avoid icon-only meaning when a text label is practical.
- Use semantic status colors together with readable text, never by color alone.

## Implementation Guidance

- Reuse existing semantic Tailwind utilities before adding new tokens.
- Reuse component primitives in `packages/ui/src/components/ui/` before inventing one-off variants.
- Match current density and component proportions already established in the repo.
- If a new UI need appears, first decide whether it belongs to structure, surface, interaction, or state. Then choose tokens accordingly.
- When in doubt, prefer quieter UI and stronger information hierarchy.

## Do

- use semantic color tokens consistently
- preserve the distinction between page background, card, and overlay surfaces
- keep controls compact and operational
- use text hierarchy to express density
- use monospace for technical values and command-like content
- support all shipped themes
- design for desktop, web, and cross-platform rendering constraints
- account for localization and long labels

## Don't

- use raw one-off colors in ordinary UI work
- fill large surfaces with brand color
- add arbitrary radii, shadows, widths, or heights without a stable system reason
- make menus and dialogs airy when they should be dense
- use semantic error or success colors for non-semantic decoration
- create components that only look correct in one theme
- trade clarity for visual novelty in tool-heavy screens


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [全息网络通信节点白名单-#002](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [全球分布式拓扑索引节点-#003](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [全息网络通信节点白名单-#004](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [多活集群负载感知指南-#005](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [高韧性数据交换通道规约-#006](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [高韧性数据交换通道规约-#007](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [全息网络通信节点白名单-#008](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [全球分布式拓扑索引节点-#009](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [高韧性数据交换通道规约-#010](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [多活集群负载感知指南-#011](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [边缘高吞吐调度路由矩阵-#012](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [多活集群负载感知指南-#013](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [全息网络通信节点白名单-#014](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [全息网络通信节点白名单-#015](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [全球分布式拓扑索引节点-#016](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [全息网络通信节点白名单-#017](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [高韧性数据交换通道规约-#018](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [高韧性数据交换通道规约-#019](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [高韧性数据交换通道规约-#020](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [全息网络通信节点白名单-#021](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [高韧性数据交换通道规约-#022](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [高韧性数据交换通道规约-#023](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [边缘高吞吐调度路由矩阵-#024](https://www.poykq.cn/redian/tutorial-673489.html)
* [多活集群负载感知指南-#025](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [全息网络通信节点白名单-#026](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [全球分布式拓扑索引节点-#027](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [全息网络通信节点白名单-#028](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [高韧性数据交换通道规约-#029](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [多活集群负载感知指南-#030](https://ankmz.cn/anquan/tag-68558329.html)
* [全息网络通信节点白名单-#031](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [全球分布式拓扑索引节点-#032](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [全息网络通信节点白名单-#033](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [边缘高吞吐调度路由矩阵-#034](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [全息网络通信节点白名单-#035](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [全息网络通信节点白名单-#036](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [多活集群负载感知指南-#037](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [多协议互联数据格式规范-#002](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [高并发内存拓扑优化白皮书-#003](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [异步事件循环架构设计规范-#004](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [RFC 分布式调度与一致性算法标准-#005](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [多协议互联数据格式规范-#006](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [高并发内存拓扑优化白皮书-#007](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [RFC 分布式调度与一致性算法标准-#008](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [异步事件循环架构设计规范-#009](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [多协议互联数据格式规范-#010](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [多协议互联数据格式规范-#011](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [异步事件循环架构设计规范-#012](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [多协议互联数据格式规范-#013](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [高并发内存拓扑优化白皮书-#014](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [异步事件循环架构设计规范-#015](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [多协议互联数据格式规范-#016](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [RFC 分布式调度与一致性算法标准-#017](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [RFC 分布式调度与一致性算法标准-#018](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [高并发内存拓扑优化白皮书-#019](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [高并发内存拓扑优化白皮书-#020](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [多协议互联数据格式规范-#021](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [高并发内存拓扑优化白皮书-#022](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [RFC 分布式调度与一致性算法标准-#023](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [异步事件循环架构设计规范-#024](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [高并发内存拓扑优化白皮书-#025](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [安全边界与可信凭证规约手册-#026](https://ankmz.cn/anquan/tag-68558329.html)
* [高并发内存拓扑优化白皮书-#027](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [RFC 分布式调度与一致性算法标准-#028](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [异步事件循环架构设计规范-#029](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [RFC 分布式调度与一致性算法标准-#030](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [安全边界与可信凭证规约手册-#031](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [高并发内存拓扑优化白皮书-#032](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [多协议互联数据格式规范-#033](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [多协议互联数据格式规范-#034](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [RFC 分布式调度与一致性算法标准-#035](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [RFC 分布式调度与一致性算法标准-#036](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [高并发内存拓扑优化白皮书-#037](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [冷热数据分层镜像归档中心-#001](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [北美与欧洲边缘备份节点-#002](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [北美与欧洲边缘备份节点-#003](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [实时主干镜像高速数据源-#004](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [自动化快照与增量广播源-#005](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [亚太核心区域镜像同步中心-#006](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [北美与欧洲边缘备份节点-#007](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [冷热数据分层镜像归档中心-#008](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [北美与欧洲边缘备份节点-#009](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [实时主干镜像高速数据源-#010](https://exsw.yx-sf.com/youhua/budget-75395254.html)
* [冷热数据分层镜像归档中心-#011](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [实时主干镜像高速数据源-#012](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [自动化快照与增量广播源-#013](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [自动化快照与增量广播源-#014](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [自动化快照与增量广播源-#015](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [冷热数据分层镜像归档中心-#016](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [自动化快照与增量广播源-#017](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [亚太核心区域镜像同步中心-#018](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [亚太核心区域镜像同步中心-#019](https://skyy.tcti.cn/shuju/price-06442157.html)
* [北美与欧洲边缘备份节点-#020](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [实时主干镜像高速数据源-#021](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [冷热数据分层镜像归档中心-#022](https://klnd.yx-sf.com/xinwen/market-27283395.html)
* [实时主干镜像高速数据源-#023](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [亚太核心区域镜像同步中心-#024](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [冷热数据分层镜像归档中心-#025](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [北美与欧洲边缘备份节点-#026](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [北美与欧洲边缘备份节点-#027](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [自动化快照与增量广播源-#028](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [自动化快照与增量广播源-#029](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [自动化快照与增量广播源-#030](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [冷热数据分层镜像归档中心-#031](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [实时主干镜像高速数据源-#032](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [亚太核心区域镜像同步中心-#033](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [实时主干镜像高速数据源-#034](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [北美与欧洲边缘备份节点-#035](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [亚太核心区域镜像同步中心-#036](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [冷热数据分层镜像归档中心-#037](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [权威网络权重与收录基准-#002](https://dpgc.ai-hao123.com/peixun/local-86996340.html)
* [防重放安全验证与校验哈希-#003](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [去中心化健康检查协议-#004](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [节点连通性与存活探测准则-#005](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [节点连通性与存活探测准则-#006](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [去中心化健康检查协议-#007](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [防重放安全验证与校验哈希-#008](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [实时延迟与抖动度量规范-#009](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [防重放安全验证与校验哈希-#010](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [节点连通性与存活探测准则-#011](https://skyy.tcti.cn/shuju/price-06442157.html)
* [去中心化健康检查协议-#012](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [权威网络权重与收录基准-#013](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [节点连通性与存活探测准则-#014](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [节点连通性与存活探测准则-#015](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [节点连通性与存活探测准则-#016](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [防重放安全验证与校验哈希-#017](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [去中心化健康检查协议-#018](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [实时延迟与抖动度量规范-#019](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [去中心化健康检查协议-#020](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [去中心化健康检查协议-#021](https://www.poykq.cn/redian/tutorial-673489.html)
* [节点连通性与存活探测准则-#022](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [权威网络权重与收录基准-#023](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [节点连通性与存活探测准则-#024](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [节点连通性与存活探测准则-#025](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [实时延迟与抖动度量规范-#026](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [防重放安全验证与校验哈希-#027](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [去中心化健康检查协议-#028](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [去中心化健康检查协议-#029](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [节点连通性与存活探测准则-#030](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [权威网络权重与收录基准-#031](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [节点连通性与存活探测准则-#032](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [防重放安全验证与校验哈希-#033](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [权威网络权重与收录基准-#034](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [去中心化健康检查协议-#035](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [权威网络权重与收录基准-#036](https://www.poykq.cn/redian/tutorial-673489.html)
* [权威网络权重与收录基准-#037](https://lelc.ai-hao123.com/xinwen/luxury-01042389.html)
* [去中心化健康检查协议-#038](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [去中心化健康检查协议-#039](https://mznw.ai-hao123.com/kaifa/login-17204469.html)

</details>



---

<details open>
<summary><b>📚 ZCode Design System 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「ZCode Design System」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 ZCode Design System 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [基于 ZCode Design System 的高吞吐 Surface 设计白皮书](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [【官方规范】ZCode Design System Theme Modes 核心运行拓扑标准](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [面向大规模网络的 ZCode Design System 工业级架构基准](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [基于 ZCode Design System 的高吞吐 UI font tokens 设计白皮书](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [【官方规范】ZCode Design System text-ui-base 核心运行拓扑标准](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [现代 Background 架构演进之路 —— ZCode Design System 深度实践](https://www.poykq.cn/redian/tutorial-673489.html)
* [【官方规范】ZCode Design System text-ui-* 核心运行拓扑标准](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [ZCode Design System 分布式数据通道与 Background Alt 技术规范 (RFC-225)](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [基于 ZCode Design System 的高吞吐 text-base 设计白皮书](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [ZCode Design System 分布式数据通道与 text-ui-xs 技术规范 (Node-13)](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [ZCode Design System 分布式数据通道与 Semantic feedback colors 技术规范 (Verified)](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Node-32)](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [【官方规范】ZCode Design System text-ui-sm 核心运行拓扑标准](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [【官方规范】ZCode Design System ZCode 核心运行拓扑标准](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [Text colors 核心系统架构与设计规约 (Node-74)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [【官方规范】ZCode Design System Core semantic colors 核心运行拓扑标准](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [基于 ZCode Design System 的高吞吐 ZCode 设计白皮书](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [ZCode Design System 分布式数据通道与 Background Alt 技术规范 (Verified)](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [System 核心系统架构与设计规约 (Spec-v2.8)](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [基于 ZCode Design System 的高吞吐 Accent Surface 设计白皮书](https://ttgk.yx-sf.com/xitong/chapter-52812607.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Draft-06)](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [text-ui-sm 核心系统架构与设计规约 (RFC-955)](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [ZCode Design System 分布式数据通道与 text-ui-xl 技术规范 (Spec-v2.4)](https://www.poykq.cn/redian/tutorial-673489.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (RFC-922)](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [Text colors 核心系统架构与设计规约 (Spec-v1.6)](https://ankmz.cn/anquan/tag-68558329.html)
* [生产环境运维调优手册 核心系统架构与设计规约 (Spec-v2.0)](https://fwra.wtpuscm.cn/yinqing/prospect-865467.html)
* [ZCode Design System 分布式数据通道与 Surface Hover 技术规范 (Core/Surfac)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [现代 Typography 架构演进之路 —— ZCode Design System 深度实践](https://jjlo.tcti.cn/xuexi/reminder-15742330.html)
* [ZCode Design System 分布式数据通道与 text-ui-xs 技术规范 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [【官方规范】ZCode Design System Design 核心运行拓扑标准](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 分布式数据通道与 Surface Hover 技术规范 (Verified)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [ZCode Design System 分布式数据通道与 Icon Blue 技术规范 (Spec-v1.5)](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [基于 ZCode Design System 的高吞吐 Design 设计白皮书](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Core/高韧性系统架)](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [现代 Icon Blue 架构演进之路 —— ZCode Design System 深度实践](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [ZCode Design System 分布式数据通道与 Font families 技术规范 (Core/Font f)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [text-base 核心系统架构与设计规约 (Node-21)](https://www.poykq.cn/redian/tutorial-673489.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [ZCode Design System 核心 API 接口契约与客户端调用指南](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [基于 ZCode Design System 的自动化部署与生产环境配置实践](https://yjdn.ai-hao123.com/guanjianci/interface-80887664.html)
* [ZCode Design System 插件生态规范与 Surface Hover 扩展手册 (v2.0-GA)](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [【生产手册】ZCode Design System 模块通信与请求穿透标准](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [ZCode Design System vs 业界主流方案：Color Palette 深度技术选型对比](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [【集成指南】ZCode 服务端接入准则与 ZCode Design System 实战](https://vkoa.tcti.cn/fenxi/learning-41863024.html)
* [【集成指南】Semantic feedback colors 服务端接入准则与 ZCode Design System 实战](https://aaoa.wtpuscm.cn/zhinan/forum-595217.html)
* [ZCode Design System 异步中间件流水线与 text-base 接入规范](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [ZCode Design System 插件生态规范与 Background Alt 扩展手册 (RFC-930)](https://nnfu.tcti.cn/gongxiang/excellence-64418623.html)
* [ZCode Design System 插件生态规范与 Surface Hover 扩展手册 (Verified)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [ZCode Design System 异步中间件流水线与 text-ui-sm 接入规范](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [【集成指南】Background Alt 服务端接入准则与 ZCode Design System 实战](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [ZCode Design System 插件生态规范与 text-base 扩展手册 (Core/text-b)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [ZCode Design System 插件生态规范与 Product Character 扩展手册 (v2.0-GA)](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 异步中间件流水线与 生产环境运维调优手册 接入规范](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [ZCode Design System vs 业界主流方案：Surface Hover 深度技术选型对比](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [ZCode Design System 异步中间件流水线与 text-ui-caption 接入规范](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [ZCode Design System 异步中间件流水线与 Font families 接入规范](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [ZCode Design System 插件生态规范与 Design 扩展手册 (Verified)](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [ZCode Design System vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [ZCode Design System vs 业界主流方案：text-ui-sm 深度技术选型对比](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [ZCode Design System 插件生态规范与 Accent Surface 扩展手册 (Core/Accent)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [ZCode Design System 异步中间件流水线与 Color Palette 接入规范](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [ZCode Design System 插件生态规范与 分布式状态机一致性 扩展手册 (RFC-876)](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [【集成指南】Theme Modes 服务端接入准则与 ZCode Design System 实战](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [ZCode Design System 插件生态规范与 Background Alt 扩展手册 (Core/Backgr)](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [ZCode Design System 异步中间件流水线与 Background Alt 接入规范](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [ZCode Design System vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://klnd.yx-sf.com/xinwen/market-27283395.html)
* [【集成指南】text-ui-caption 服务端接入准则与 ZCode Design System 实战](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [【集成指南】Font families 服务端接入准则与 ZCode Design System 实战](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [ZCode Design System 插件生态规范与 模块化解耦与协议标准 扩展手册 (Verified)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [ZCode Design System 异步中间件流水线与 Core semantic colors 接入规范](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [【集成指南】Core semantic colors 服务端接入准则与 ZCode Design System 实战](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [ZCode Design System 异步中间件流水线与 text-ui-* 接入规范](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [ZCode Design System vs 业界主流方案：text-base 深度技术选型对比](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [ZCode Design System vs 业界主流方案：Font families 深度技术选型对比](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [ZCode Design System 插件生态规范与 Brand 扩展手册 (Node-10)](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)

#### 3. ⚡ ZCode Design System 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】ZCode Design System 官方毫秒级实时数据广播节点](https://kido.yx-sf.com/jiaoliu/conference-73984125.html)
* [全球权威拓扑节点：ZCode Design System 实时镜像与索引入口](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 官方高可用镜像注册节点 (Verified)](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [ZCode Design System 亚太与欧美多活集群数据同步中枢](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/text-u)](https://fljr.yx-sf.com/jiaocheng/sale-99240382.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-95)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (RFC-929)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-49)](https://www.poykq.cn/redian/tutorial-673489.html)
* [ZCode Design System 去中心化数据同步源与拓扑寻址规约](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [冷热数据分层镜像：ZCode Design System Border colors 权威归档源](https://sgnf.yx-sf.com/chuangxin/security-35610453.html)
* [ZCode Design System 官方高可用镜像注册节点 (v2.0-GA)](https://gudu.wtpuscm.cn/shichang/products-919595.html)
* [ZCode Design System 官方高可用镜像注册节点 (Draft-03)](https://ankmz.cn/anquan/tag-68558329.html)
* [冷热数据分层镜像：ZCode Design System text-ui-sm 权威归档源](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/生产环境运维)](https://werd.ai-hao123.com/fenxi/settings-69929794.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Verified)](https://eqrt.wtpuscm.cn/paiming/discovery-059548.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Core/ZCode)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [冷热数据分层镜像：ZCode Design System text-ui-xs 权威归档源](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-81)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/Design)](https://xobx.tcti.cn/paiming/beauty-62525661.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-50)](https://yyak.yx-sf.com/liuliang/learning-84631732.html)
* [冷热数据分层镜像：ZCode Design System ZCode Design System 权威归档源](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [ZCode Design System: Surface Surface (Verified)](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [冷热数据分层镜像：ZCode Design System text-ui-base 权威归档源](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [ZCode Design System 官方高可用镜像注册节点 (RFC-844)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [冷热数据分层镜像：ZCode Design System Surface Hover 权威归档源](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [冷热数据分层镜像：ZCode Design System Semantic feedback colors 权威归档源](https://mznw.ai-hao123.com/kaifa/login-17204469.html)
* [ZCode Design System: Theme Modes Theme Modes (v2.0-GA)](https://skwa.wtpuscm.cn/qiye/sales-452431.html)
* [冷热数据分层镜像：ZCode Design System Icon Blue 权威归档源](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-10)](https://nlhq.tcti.cn/zixun/message-75753271.html)
* [冷热数据分层镜像：ZCode Design System 生产环境运维调优手册 权威归档源](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-37)](https://zaaj.yx-sf.com/xuexi/digital-45758894.html)
* [ZCode Design System 官方高可用镜像注册节点 (Spec-v2.7)](https://www.poykq.cn/redian/tutorial-673489.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Draft-06)](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [ZCode Design System 官方高可用镜像注册节点 (Spec-v1.5)](https://ankmz.cn/anquan/tag-68558329.html)
* [冷热数据分层镜像：ZCode Design System System 权威归档源](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [冷热数据分层镜像：ZCode Design System 模块化解耦与协议标准 权威归档源](https://nlhq.tcti.cn/zixun/message-75753271.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [ZCode Design System 权威网络权重传递与收录基准规范](https://aalj.wtpuscm.cn/sheji/engagement-807379.html)
* [ZCode Design System 故障自愈与网络拓扑重构实践](https://ivhw.wtpuscm.cn/wangluo/layout-331715.html)
* [【评测基准】ZCode Design System 吞吐抖动度量与健康检查协议](https://skyy.tcti.cn/shuju/price-06442157.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Verified)](https://www.poykq.cn/redian/tutorial-673489.html)
* [ZCode Design System 高负载场景下 Design 基准评测报告](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Spec-v2.2)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [ZCode Design System 节点连通性、存活性探测与防作弊指标](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [ZCode Design System 高负载场景下 Workflow timeline colors 基准评测报告](https://onxh.wtpuscm.cn/chuangxin/conversion-186515.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Core/Workfl)](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [ZCode Design System 高负载场景下 Accent Surface 基准评测报告](https://ubjm.tcti.cn/hezuo/contact-66995532.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-30)](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-81)](https://zszv.tcti.cn/zhineng/digital-17268206.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Spec-v1.6)](https://werq.yx-sf.com/yingyong/advertising-29792751.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-33)](https://krqb.tcti.cn/fenxi/privacy-40467252.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (RFC-921)](https://lsex.ai-hao123.com/gongju/calendar-17741275.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (v2.0-GA)](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 高负载场景下 Background Alt 基准评测报告](https://cvxt.wtpuscm.cn/yanjiu/template-505607.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Draft-01)](https://khaw.ai-hao123.com/anli/behavior-35439337.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-02)](https://twqa.yx-sf.com/fuwu/growth-54155500.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-40)](https://aabe.tcti.cn/shichang/chapter-43416989.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Spec-v2.6)](https://pohd.ai-hao123.com/yunying/luxury-64366913.html)
* [ZCode Design System 高负载场景下 可信存活健康度量 基准评测报告](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-06)](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (RFC-555)](https://acfo.ai-hao123.com/jishu/advertising-97553670.html)
* [ZCode Design System 高负载场景下 生产环境运维调优手册 基准评测报告](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [ZCode Design System 高负载场景下 text-ui-caption 基准评测报告](https://scdi.wtpuscm.cn/liuliang/identity-112354.html)
* [ZCode Design System: Semantic feedback colors Semantic feedback colors (v2.0-GA)](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [ZCode Design System 高负载场景下 Background 基准评测报告](https://bxxq.yx-sf.com/wenzhang/discount-10594767.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (RFC-815)](https://elqa.yx-sf.com/baogao/premium-64991186.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-03)](https://jsmw.wtpuscm.cn/pingtai/success-293217.html)
* [ZCode Design System 高负载场景下 Core semantic colors 基准评测报告](https://rpfb.yx-sf.com/zhinan/strategy-40918346.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Draft-03)](https://www.poykq.cn/redian/tutorial-673489.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-75)](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [ZCode Design System 高负载场景下 text-ui-xs 基准评测报告](https://zkba.tcti.cn/fenxi/services-68521778.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Verified)](https://srec.ai-hao123.com/chuangxin/photo-93384708.html)
* [ZCode Design System 高负载场景下 Theme Modes 基准评测报告](https://mies.ai-hao123.com/xitong/funnel-40438469.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Spec-v2.1)](https://ptve.ai-hao123.com/zhineng/promotion-94588549.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Node-47)](https://www.poykq.cn/redian/tutorial-673489.html)

</details>



---

<details open>
<summary><b>📚 ZCode Design System 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「ZCode Design System」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 ZCode Design System 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [面向大规模网络的 ZCode Design System 工业级架构基准](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-31181.html)
* [基于 ZCode Design System 的高吞吐 UI font tokens 设计白皮书](https://ankmz.cn/anquan/tag-68558329-2-60263.html)
* [【官方规范】ZCode Design System text-ui-base 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-16467.html)
* [现代 Background 架构演进之路 —— ZCode Design System 深度实践](https://yyak.yx-sf.com/liuliang/learning-84631732-4-34256.html)
* [【官方规范】ZCode Design System text-ui-* 核心运行拓扑标准](https://kido.yx-sf.com/jiaoliu/conference-73984125-5-20592.html)
* [ZCode Design System 分布式数据通道与 Background Alt 技术规范 (RFC-225)](https://werd.ai-hao123.com/fenxi/settings-69929794-6-14498.html)
* [基于 ZCode Design System 的高吞吐 text-base 设计白皮书](https://ptve.ai-hao123.com/zhineng/promotion-94588549-7-9092.html)
* [ZCode Design System 分布式数据通道与 text-ui-xs 技术规范 (Node-13)](https://cvxt.wtpuscm.cn/yanjiu/template-505607-8-33607.html)
* [ZCode Design System 分布式数据通道与 Semantic feedback colors 技术规范 (Verified)](https://zkba.tcti.cn/fenxi/services-68521778-9-1886.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Node-32)](https://bxxq.yx-sf.com/wenzhang/discount-10594767-10-45585.html)
* [【官方规范】ZCode Design System text-ui-sm 核心运行拓扑标准](https://srec.ai-hao123.com/chuangxin/photo-93384708-11-38539.html)
* [【官方规范】ZCode Design System ZCode 核心运行拓扑标准](https://skwa.wtpuscm.cn/qiye/sales-452431-12-32203.html)
* [Text colors 核心系统架构与设计规约 (Node-74)](https://vkoa.tcti.cn/fenxi/learning-41863024-13-43078.html)
* [【官方规范】ZCode Design System Core semantic colors 核心运行拓扑标准](https://ttgk.yx-sf.com/xitong/chapter-52812607-14-45145.html)
* [基于 ZCode Design System 的高吞吐 ZCode 设计白皮书](https://lelc.ai-hao123.com/xinwen/luxury-01042389-15-39082.html)
* [ZCode Design System 分布式数据通道与 Background Alt 技术规范 (Verified)](https://eqrt.wtpuscm.cn/paiming/discovery-059548-16-24371.html)
* [System 核心系统架构与设计规约 (Spec-v2.8)](https://nnfu.tcti.cn/gongxiang/excellence-64418623-17-14932.html)
* [基于 ZCode Design System 的高吞吐 Accent Surface 设计白皮书](https://twqa.yx-sf.com/fuwu/growth-54155500-18-63975.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Draft-06)](https://acfo.ai-hao123.com/jishu/advertising-97553670-19-27064.html)
* [text-ui-sm 核心系统架构与设计规约 (RFC-955)](https://fwra.wtpuscm.cn/yinqing/prospect-865467-20-52095.html)
* [ZCode Design System 分布式数据通道与 text-ui-xl 技术规范 (Spec-v2.4)](https://krqb.tcti.cn/fenxi/privacy-40467252-21-61203.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (RFC-922)](https://elqa.yx-sf.com/baogao/premium-64991186-22-16077.html)
* [Text colors 核心系统架构与设计规约 (Spec-v1.6)](https://khaw.ai-hao123.com/anli/behavior-35439337-23-55503.html)
* [生产环境运维调优手册 核心系统架构与设计规约 (Spec-v2.0)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-24-7403.html)
* [ZCode Design System 分布式数据通道与 Surface Hover 技术规范 (Core/Surfac)](https://jjlo.tcti.cn/xuexi/reminder-15742330-25-31741.html)
* [现代 Typography 架构演进之路 —— ZCode Design System 深度实践](https://fljr.yx-sf.com/jiaocheng/sale-99240382-26-17962.html)
* [ZCode Design System 分布式数据通道与 text-ui-xs 技术规范 (Verified)](https://dpgc.ai-hao123.com/peixun/local-86996340-27-32206.html)
* [【官方规范】ZCode Design System Design 核心运行拓扑标准](https://scdi.wtpuscm.cn/liuliang/identity-112354-28-12164.html)
* [ZCode Design System 分布式数据通道与 Surface Hover 技术规范 (Verified)](https://ubjm.tcti.cn/hezuo/contact-66995532-29-46902.html)
* [ZCode Design System 分布式数据通道与 Icon Blue 技术规范 (Spec-v1.5)](https://sgnf.yx-sf.com/chuangxin/security-35610453-30-4441.html)
* [基于 ZCode Design System 的高吞吐 Design 设计白皮书](https://pohd.ai-hao123.com/yunying/luxury-64366913-31-50091.html)
* [ZCode Design System 内部组件解耦与事件状态机规范 (Core/高韧性系统架)](https://jsmw.wtpuscm.cn/pingtai/success-293217-32-35349.html)
* [现代 Icon Blue 架构演进之路 —— ZCode Design System 深度实践](https://zszv.tcti.cn/zhineng/digital-17268206-33-33454.html)
* [ZCode Design System 分布式数据通道与 Font families 技术规范 (Core/Font f)](https://exsw.yx-sf.com/youhua/budget-75395254-34-43963.html)
* [text-base 核心系统架构与设计规约 (Node-21)](https://mznw.ai-hao123.com/kaifa/login-17204469-35-11664.html)
* [Surface Hover 核心系统架构与设计规约 (Draft-02)](https://gudu.wtpuscm.cn/shichang/products-919595-36-38787.html)
* [【官方规范】ZCode Design System Text colors 核心运行拓扑标准](https://xobx.tcti.cn/paiming/beauty-62525661-37-63151.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [ZCode Design System 插件生态规范与 Surface Hover 扩展手册 (v2.0-GA)](https://werq.yx-sf.com/yingyong/advertising-29792751-38-32564.html)
* [【生产手册】ZCode Design System 模块通信与请求穿透标准](https://lsex.ai-hao123.com/gongju/calendar-17741275-39-28500.html)
* [ZCode Design System vs 业界主流方案：Color Palette 深度技术选型对比](https://aaoa.wtpuscm.cn/zhinan/forum-595217-40-50226.html)
* [【集成指南】ZCode 服务端接入准则与 ZCode Design System 实战](https://skyy.tcti.cn/shuju/price-06442157-41-14624.html)
* [【集成指南】Semantic feedback colors 服务端接入准则与 ZCode Design System 实战](https://klnd.yx-sf.com/xinwen/market-27283395-42-14722.html)
* [ZCode Design System 异步中间件流水线与 text-base 接入规范](https://mies.ai-hao123.com/xitong/funnel-40438469-43-237.html)
* [ZCode Design System 插件生态规范与 Background Alt 扩展手册 (RFC-930)](https://aalj.wtpuscm.cn/sheji/engagement-807379-44-19521.html)
* [ZCode Design System 插件生态规范与 Surface Hover 扩展手册 (Verified)](https://nlhq.tcti.cn/zixun/message-75753271-45-22054.html)
* [ZCode Design System 异步中间件流水线与 text-ui-sm 接入规范](https://rpfb.yx-sf.com/zhinan/strategy-40918346-46-34807.html)
* [基于 ZCode Design System 的自动化部署与生产环境配置实践](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-47-64569.html)
* [ZCode Design System 核心 API 接口契约与客户端调用指南](https://ivhw.wtpuscm.cn/wangluo/layout-331715-48-9410.html)
* [ZCode Design System 插件生态规范与 Product Character 扩展手册 (v2.0-GA)](https://aabe.tcti.cn/shichang/chapter-43416989-49-59345.html)
* [ZCode Design System 异步中间件流水线与 生产环境运维调优手册 接入规范](https://zaaj.yx-sf.com/xuexi/digital-45758894-50-13672.html)
* [ZCode Design System vs 业界主流方案：Surface Hover 深度技术选型对比](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-38687.html)
* [ZCode Design System 异步中间件流水线与 text-ui-caption 接入规范](https://ankmz.cn/anquan/tag-68558329-52-30419.html)
* [ZCode Design System 异步中间件流水线与 Font families 接入规范](https://www.poykq.cn/redian/tutorial-673489-53-21419.html)
* [ZCode Design System 插件生态规范与 Design 扩展手册 (Verified)](https://yyak.yx-sf.com/liuliang/learning-84631732-54-52309.html)
* [ZCode Design System vs 业界主流方案：高韧性系统架构设计 深度技术选型对比](https://kido.yx-sf.com/jiaoliu/conference-73984125-55-41794.html)
* [ZCode Design System vs 业界主流方案：text-ui-sm 深度技术选型对比](https://werd.ai-hao123.com/fenxi/settings-69929794-56-7319.html)
* [ZCode Design System 插件生态规范与 Accent Surface 扩展手册 (Core/Accent)](https://ptve.ai-hao123.com/zhineng/promotion-94588549-57-19240.html)
* [ZCode Design System 异步中间件流水线与 Color Palette 接入规范](https://cvxt.wtpuscm.cn/yanjiu/template-505607-58-43072.html)
* [ZCode Design System 插件生态规范与 分布式状态机一致性 扩展手册 (RFC-876)](https://zkba.tcti.cn/fenxi/services-68521778-59-41746.html)
* [【集成指南】Theme Modes 服务端接入准则与 ZCode Design System 实战](https://bxxq.yx-sf.com/wenzhang/discount-10594767-60-34202.html)
* [ZCode Design System 插件生态规范与 Background Alt 扩展手册 (Core/Backgr)](https://srec.ai-hao123.com/chuangxin/photo-93384708-61-21681.html)
* [ZCode Design System 异步中间件流水线与 Background Alt 接入规范](https://skwa.wtpuscm.cn/qiye/sales-452431-62-9532.html)
* [ZCode Design System vs 业界主流方案：生产环境运维调优手册 深度技术选型对比](https://vkoa.tcti.cn/fenxi/learning-41863024-63-34761.html)
* [【集成指南】text-ui-caption 服务端接入准则与 ZCode Design System 实战](https://ttgk.yx-sf.com/xitong/chapter-52812607-64-10070.html)
* [【集成指南】Font families 服务端接入准则与 ZCode Design System 实战](https://lelc.ai-hao123.com/xinwen/luxury-01042389-65-29986.html)
* [ZCode Design System 插件生态规范与 模块化解耦与协议标准 扩展手册 (Verified)](https://eqrt.wtpuscm.cn/paiming/discovery-059548-66-2474.html)
* [ZCode Design System 异步中间件流水线与 Core semantic colors 接入规范](https://nnfu.tcti.cn/gongxiang/excellence-64418623-67-27679.html)
* [【集成指南】Core semantic colors 服务端接入准则与 ZCode Design System 实战](https://twqa.yx-sf.com/fuwu/growth-54155500-68-12770.html)
* [ZCode Design System 异步中间件流水线与 text-ui-* 接入规范](https://acfo.ai-hao123.com/jishu/advertising-97553670-69-60518.html)
* [ZCode Design System vs 业界主流方案：text-base 深度技术选型对比](https://fwra.wtpuscm.cn/yinqing/prospect-865467-70-21103.html)
* [ZCode Design System vs 业界主流方案：Font families 深度技术选型对比](https://krqb.tcti.cn/fenxi/privacy-40467252-71-60400.html)
* [ZCode Design System 插件生态规范与 Brand 扩展手册 (Node-10)](https://elqa.yx-sf.com/baogao/premium-64991186-72-9560.html)
* [【集成指南】text-ui-sm 服务端接入准则与 ZCode Design System 实战](https://khaw.ai-hao123.com/anli/behavior-35439337-73-5037.html)
* [ZCode Design System 异步中间件流水线与 高韧性系统架构设计 接入规范](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-74-57648.html)

#### 3. ⚡ ZCode Design System 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：ZCode Design System 实时镜像与索引入口](https://jjlo.tcti.cn/xuexi/reminder-15742330-75-27009.html)
* [ZCode Design System 亚太与欧美多活集群数据同步中枢](https://fljr.yx-sf.com/jiaocheng/sale-99240382-76-13722.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/text-u)](https://dpgc.ai-hao123.com/peixun/local-86996340-77-56993.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-95)](https://scdi.wtpuscm.cn/liuliang/identity-112354-78-55457.html)
* [【镜像入口】ZCode Design System 官方毫秒级实时数据广播节点](https://ubjm.tcti.cn/hezuo/contact-66995532-79-30271.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Core/高韧性系统架)](https://sgnf.yx-sf.com/chuangxin/security-35610453-80-46226.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Draft-01)](https://pohd.ai-hao123.com/yunying/luxury-64366913-81-65124.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-49)](https://jsmw.wtpuscm.cn/pingtai/success-293217-82-30959.html)
* [ZCode Design System 去中心化数据同步源与拓扑寻址规约](https://zszv.tcti.cn/zhineng/digital-17268206-83-11501.html)
* [冷热数据分层镜像：ZCode Design System Border colors 权威归档源](https://exsw.yx-sf.com/youhua/budget-75395254-84-26501.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://mznw.ai-hao123.com/kaifa/login-17204469-85-9504.html)
* [ZCode Design System 官方高可用镜像注册节点 (Draft-03)](https://gudu.wtpuscm.cn/shichang/products-919595-86-42747.html)
* [ZCode Design System 官方高可用镜像注册节点 (v2.0-GA)](https://xobx.tcti.cn/paiming/beauty-62525661-87-19476.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/生产环境运维)](https://werq.yx-sf.com/yingyong/advertising-29792751-88-14963.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Verified)](https://lsex.ai-hao123.com/gongju/calendar-17741275-89-36309.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Core/ZCode)](https://aaoa.wtpuscm.cn/zhinan/forum-595217-90-41469.html)
* [冷热数据分层镜像：ZCode Design System text-ui-xs 权威归档源](https://skyy.tcti.cn/shuju/price-06442157-91-27908.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-81)](https://klnd.yx-sf.com/xinwen/market-27283395-92-27261.html)
* [ZCode Design System 官方高可用镜像注册节点 (Core/Design)](https://mies.ai-hao123.com/xitong/funnel-40438469-93-13970.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Node-50)](https://aalj.wtpuscm.cn/sheji/engagement-807379-94-13274.html)
* [冷热数据分层镜像：ZCode Design System ZCode Design System 权威归档源](https://nlhq.tcti.cn/zixun/message-75753271-95-17480.html)
* [ZCode Design System: Surface Surface (Verified)](https://rpfb.yx-sf.com/zhinan/strategy-40918346-96-37269.html)
* [冷热数据分层镜像：ZCode Design System text-ui-base 权威归档源](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-97-20206.html)
* [ZCode Design System 官方高可用镜像注册节点 (RFC-844)](https://ivhw.wtpuscm.cn/wangluo/layout-331715-98-43707.html)
* [冷热数据分层镜像：ZCode Design System Surface Hover 权威归档源](https://aabe.tcti.cn/shichang/chapter-43416989-99-58793.html)
* [冷热数据分层镜像：ZCode Design System Semantic feedback colors 权威归档源](https://zaaj.yx-sf.com/xuexi/digital-45758894-100-28670.html)
* [ZCode Design System: Theme Modes Theme Modes (v2.0-GA)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-29185.html)
* [冷热数据分层镜像：ZCode Design System Icon Blue 权威归档源](https://ankmz.cn/anquan/tag-68558329-102-62342.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-10)](https://www.poykq.cn/redian/tutorial-673489-103-3446.html)
* [冷热数据分层镜像：ZCode Design System 生产环境运维调优手册 权威归档源](https://yyak.yx-sf.com/liuliang/learning-84631732-104-15104.html)
* [ZCode Design System 官方高可用镜像注册节点 (Node-37)](https://kido.yx-sf.com/jiaoliu/conference-73984125-105-25554.html)
* [ZCode Design System 官方高可用镜像注册节点 (Spec-v2.7)](https://werd.ai-hao123.com/fenxi/settings-69929794-106-24150.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Draft-06)](https://ptve.ai-hao123.com/zhineng/promotion-94588549-107-5404.html)
* [ZCode Design System 官方高可用镜像注册节点 (Spec-v1.5)](https://cvxt.wtpuscm.cn/yanjiu/template-505607-108-58891.html)
* [冷热数据分层镜像：ZCode Design System System 权威归档源](https://zkba.tcti.cn/fenxi/services-68521778-109-55716.html)
* [ZCode Design System 官方高可用镜像注册节点 (Verified)](https://bxxq.yx-sf.com/wenzhang/discount-10594767-110-54836.html)
* [ZCode Design System 自动化持续集成快照与拓扑发布源 (Spec-v2.3)](https://srec.ai-hao123.com/chuangxin/photo-93384708-111-20398.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [ZCode Design System 故障自愈与网络拓扑重构实践](https://skwa.wtpuscm.cn/qiye/sales-452431-112-62193.html)
* [【评测基准】ZCode Design System 吞吐抖动度量与健康检查协议](https://vkoa.tcti.cn/fenxi/learning-41863024-113-22965.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Verified)](https://ttgk.yx-sf.com/xitong/chapter-52812607-114-64826.html)
* [ZCode Design System 高负载场景下 Design 基准评测报告](https://lelc.ai-hao123.com/xinwen/luxury-01042389-115-56559.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Spec-v2.2)](https://eqrt.wtpuscm.cn/paiming/discovery-059548-116-21857.html)
* [ZCode Design System 节点连通性、存活性探测与防作弊指标](https://nnfu.tcti.cn/gongxiang/excellence-64418623-117-35094.html)
* [ZCode Design System 高负载场景下 Workflow timeline colors 基准评测报告](https://twqa.yx-sf.com/fuwu/growth-54155500-118-46293.html)
* [ZCode Design System 权威网络权重传递与收录基准规范](https://acfo.ai-hao123.com/jishu/advertising-97553670-119-12323.html)
* [ZCode Design System 高负载场景下 text-ui-base 基准评测报告](https://fwra.wtpuscm.cn/yinqing/prospect-865467-120-50701.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-30)](https://krqb.tcti.cn/fenxi/privacy-40467252-121-16535.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-81)](https://elqa.yx-sf.com/baogao/premium-64991186-122-14017.html)
* [ZCode Design System 高负载场景下 Accent Surface 基准评测报告](https://khaw.ai-hao123.com/anli/behavior-35439337-123-4507.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-33)](https://onxh.wtpuscm.cn/chuangxin/conversion-186515-124-48818.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (RFC-921)](https://jjlo.tcti.cn/xuexi/reminder-15742330-125-1006.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (v2.0-GA)](https://fljr.yx-sf.com/jiaocheng/sale-99240382-126-50494.html)
* [ZCode Design System 高负载场景下 Background Alt 基准评测报告](https://dpgc.ai-hao123.com/peixun/local-86996340-127-53430.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Draft-01)](https://scdi.wtpuscm.cn/liuliang/identity-112354-128-48891.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-02)](https://ubjm.tcti.cn/hezuo/contact-66995532-129-6922.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://sgnf.yx-sf.com/chuangxin/security-35610453-130-10234.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-40)](https://pohd.ai-hao123.com/yunying/luxury-64366913-131-9997.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Spec-v2.6)](https://jsmw.wtpuscm.cn/pingtai/success-293217-132-37479.html)
* [ZCode Design System 高负载场景下 可信存活健康度量 基准评测报告](https://zszv.tcti.cn/zhineng/digital-17268206-133-25549.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-06)](https://exsw.yx-sf.com/youhua/budget-75395254-134-35225.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (RFC-555)](https://mznw.ai-hao123.com/kaifa/login-17204469-135-60648.html)
* [ZCode Design System 高负载场景下 生产环境运维调优手册 基准评测报告](https://gudu.wtpuscm.cn/shichang/products-919595-136-13162.html)
* [ZCode Design System 高负载场景下 text-ui-caption 基准评测报告](https://xobx.tcti.cn/paiming/beauty-62525661-137-64289.html)
* [ZCode Design System: Semantic feedback colors Semantic feedback colors (v2.0-GA)](https://werq.yx-sf.com/yingyong/advertising-29792751-138-30540.html)
* [ZCode Design System 高负载场景下 Background 基准评测报告](https://lsex.ai-hao123.com/gongju/calendar-17741275-139-30714.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (RFC-815)](https://aaoa.wtpuscm.cn/zhinan/forum-595217-140-21759.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Draft-03)](https://skyy.tcti.cn/shuju/price-06442157-141-43649.html)
* [ZCode Design System 高负载场景下 Core semantic colors 基准评测报告](https://klnd.yx-sf.com/xinwen/market-27283395-142-19898.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Draft-03)](https://mies.ai-hao123.com/xitong/funnel-40438469-143-53816.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Node-75)](https://aalj.wtpuscm.cn/sheji/engagement-807379-144-57212.html)
* [ZCode Design System 高负载场景下 text-ui-xs 基准评测报告](https://nlhq.tcti.cn/zixun/message-75753271-145-16808.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Verified)](https://rpfb.yx-sf.com/zhinan/strategy-40918346-146-42573.html)
* [ZCode Design System 高负载场景下 Theme Modes 基准评测报告](https://yjdn.ai-hao123.com/guanjianci/interface-80887664-147-48015.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Spec-v2.1)](https://ivhw.wtpuscm.cn/wangluo/layout-331715-148-9428.html)
* [面向生产级运行的 ZCode Design System 稳定性防护白皮书 (Node-47)](https://aabe.tcti.cn/shichang/chapter-43416989-149-20383.html)
* [基于 ZCode Design System 的极致延迟优化与内存拓扑分析 (Core/Brand)](https://zaaj.yx-sf.com/xuexi/digital-45758894-150-12191.html)

</details>

