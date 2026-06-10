# BitPro Visual Contract

## Product Feel

BitPro pages should feel like a quiet, high-density crypto research and execution workstation. The first screen should expose useful state, controls, and diagnostics quickly. Avoid marketing-page composition, oversized hero copy, decorative gradients, abstract illustrations, large empty cards, and feature-explanation blocks.

## Layout

- Use the existing `MainLayout` shell and page-level left alignment. Avoid centered narrow wrappers for operator workspaces unless the existing page pattern already uses one.
- Prefer full-width bands, split panels, KPI grids, tables, and scrollable internal regions. Do not nest cards inside decorative parent cards.
- Give fixed-format elements stable dimensions with `grid`, `min-h`, `max-h`, `aspect-*`, or internal scroll containers so hover states, labels, data refreshes, and loading text do not move the layout.
- Keep text sizes compact inside cards, tables, filters, sidebars, and modals. Reserve large type for rare page-level headings.
- On mobile, let controls wrap cleanly and keep action buttons readable. Do not let long Chinese strategy names or symbols overlap controls.

## Theme And Tokens

- Use `bg-crypto-bg`, `bg-crypto-card`, `border-crypto-border`, gray text scales, and small blue/cyan accents already present in the repo.
- Use `text-up`, `text-down`, `bg-up`, and `bg-down` for gain/loss and PnL semantics. These follow the runtime red-up/green-down setting from CSS variables.
- Use local radius and border patterns from neighboring components. Do not introduce softer, pill-heavy, glossy, or rounded-3xl styling unless the surrounding page already has that exact pattern.
- Avoid one-note palettes and large purple/blue gradients. BitPro's dark shell should remain restrained and data-led.

## Components

- Use `lucide-react` icons rather than hand-drawn SVG for common actions and states.
- Match sidebar route icons with page header identity when adding or changing a first-level route.
- Use segmented controls for modes, tabs for views, toggles/checkboxes for binary settings, steppers/inputs/sliders for numeric settings, and menus/selects for option sets.
- Use icon-only buttons with accessible labels/tooltips for common tool actions when space is tight; use icon+text buttons for primary commands.
- Keep repeated cards scan-friendly: title, status, primary metric, compact secondary metrics, then actions. Avoid long explanatory copy inside operational cards.

## Data Integrity

- Use real service data or explicit empty/error states. Never mock market rows, strategy rows, K-lines, funding data, account values, trades, or screenshot data.
- Paper/simulation UI must not imply real account state. Live UI must clearly identify real OKX/private-account data and preserve confirmation boundaries.
- When data is pending or unavailable, show the real reason when the API provides it; do not replace backend failure detail with generic HTTP text.

## Figma And Browser Loop

- For major UI direction, generate a Figma design or other reviewable visual first, then wait for approval unless the user explicitly asked to implement an approved plan.
- When implementing from Figma, preserve BitPro's existing components and tokens before inventing new structure.
- Use Playwright or screenshots to verify real rendered pages whenever a permitted running target exists. Check at least one desktop and one narrow viewport for UI-heavy work.
- If BitPro local services are not already running and the user has not authorized starting them, rely on static verification and record browser verification as pending.
