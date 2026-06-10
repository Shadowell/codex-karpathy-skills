# BitPro Icon Contract

## Icon Sources

- Use `lucide-react` first for standard UI concepts. Search existing imports in the affected page before adding a new icon family.
- Prefer a nearby existing icon choice when the same concept already appears elsewhere.
- Do not add standalone icon libraries unless the user explicitly approves a broader design-system dependency.
- Use custom SVG only for:
  - BitPro brand or product mark.
  - First-level module identity that needs a stable custom symbol.
  - Strategy family badges where a generic lucide icon would be misleading.
  - Figma-generated icon sets approved by the user.

## Size And Stroke

- Table sort, row action, tiny status: `h-3 w-3` or `h-3.5 w-3.5`.
- Buttons, filters, tabs, search inputs, KPI captions: `h-4 w-4`.
- Card headers and section titles: `h-4 w-4` or `h-5 w-5`.
- Page title marks: `h-5 w-5` or `h-6 w-6` inside a compact shell.
- Keep lucide's default stroke style unless a surrounding component already standardizes stroke width. Avoid thick filled pictograms in the product shell.

## Color Semantics

- Use neutral gray for passive controls and metadata.
- Use blue/cyan for refresh, info, research, market data, navigation focus, and primary tool actions.
- Use amber for funding, pending, warning, attention, and cautionary statuses.
- Use red for destructive actions, critical risk, liquidation, failed states, and security-sensitive warnings.
- Use green for healthy/success/connected states only when it is not being used as configured market-down color in a way that confuses the context.
- Use `text-up` / `text-down` for market direction, PnL, return, and trend semantics.

## Containers

- Prefer compact square icon shells: `inline-flex items-center justify-center rounded-lg border border-crypto-border bg-crypto-card`.
- Keep icon containers stable in size so hover, loading, or active states do not move layouts.
- For icon-only buttons, include accessible names via `aria-label`, title, or existing tooltip pattern.
- Do not place text inside rounded icon-like badges when a recognized symbol would work better.

## Route And Page Identity

- When a page appears in the sidebar, keep the sidebar icon and first-screen page header icon conceptually identical.
- For direct routes without sidebar entry, keep the page title icon consistent with nearby module groups.
- When adding a new first-level page, update the relevant page design document with icon identity if the icon is part of the UX contract.

## Custom SVG And Figma Icons

- Design custom icons on a simple grid, preferably 24px base with centered geometry and optical balance.
- Keep the icon readable at 16px on dark backgrounds before accepting it.
- Avoid gradients, shadows, tiny text, small candlesticks, and miniature charts inside icons.
- Export custom SVGs with minimal paths, no embedded raster images, no hard-coded unrelated colors, and no inaccessible inline text.
- Before implementation, compare the custom icon against a lucide fallback. Keep the custom icon only if it communicates a BitPro-specific concept better.
