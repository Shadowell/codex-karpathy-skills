---
name: bitpro-frontend-design-system
description: 用于 BitPro 前端 UI/UX 设计、React/TSX 页面改造、Tailwind 样式调整、Figma 设计生成/还原、页面截图验收、docs/pages 页面设计文档同步。Use when Codex needs to design, redesign, polish, implement, or verify BitPro operator-facing frontend pages, cards, dashboards, modals, tables, charts, filters, navigation, or responsive layouts.
---

# BitPro Frontend Design System

## Core Workflow

1. Read the current page contract before editing: `AGENTS.md`, the relevant `docs/pages/<页面>.md`, `docs/product/specification.md`, `docs/project/progress.md`, and the active `docs/contracts/` file when the change affects behavior.
2. For UI-heavy or visual-direction work, produce a reviewable design first. Prefer Figma skills (`figma-generate-design`, then `figma-implement-design`) when available. If the user has already approved a plan or explicitly asks to implement, proceed directly.
3. Implement inside existing BitPro patterns. Reuse local components, route shells, lucide icons, spacing rhythm, theme tokens, and data contracts before creating new abstractions.
4. Keep user-visible copy Chinese-first. Localize labels, table headers, KPI names, statuses, warnings, empty states, and action text.
5. Verify with the narrowest meaningful checks. For visual work, use Playwright/screenshot validation when a permitted running target or deployed page is available; do not start or restart BitPro local long-running services unless the user explicitly asks.
6. For page-level UI or workflow changes, update the matching `docs/pages/<页面>.md`, `docs/product/specification.md` if the product contract changed, and `docs/project/progress.md` with exact validation status.

## Visual Rules

Load `references/visual-contract.md` before substantial page, dashboard, modal, table, chart, navigation, or responsive layout work.

Always preserve these BitPro defaults:

- Treat BitPro as a dense dark financial operator workspace, not a marketing site.
- Use existing theme tokens such as `bg-crypto-bg`, `bg-crypto-card`, `border-crypto-border`, `text-up`, and `text-down`; do not hard-code market gain/loss colors when semantic utilities exist.
- Use `lucide-react` icons for page headers, sidebar identity, buttons, states, and tool actions when an icon exists.
- Prefer compact information architecture: KPI rows, tables, segmented filters, scrollable panels, and stable grid dimensions over decorative hero sections.
- Show truthful states from real APIs or explicit empty/error states. Do not mock rows, inject DOM data, fabricate market values, or hide missing data behind pretty placeholders.

## Verification Checklist

Before declaring UI work complete:

- Check text wrapping, truncation, and overflow on desktop and mobile-sized viewports.
- Check that dynamic values, loading states, empty states, error states, and disabled states do not resize or overlap their containers.
- Check that market colors follow the current red-up/green-down configuration through `text-up` / `text-down`.
- Run relevant static tests, lint/build, and `git diff --check` where appropriate.
- Record any skipped browser or deployment verification explicitly in `docs/project/progress.md`.
