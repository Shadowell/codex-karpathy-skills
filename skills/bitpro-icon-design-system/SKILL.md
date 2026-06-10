---
name: bitpro-icon-design-system
description: 用于 BitPro 图标设计、lucide-react 图标选择、自定义 SVG/品牌/模块/策略标识、侧边栏与页面标题图标一致性、暗色金融 UI 图标可读性和 Figma 图标库设计。Use when Codex needs to add, replace, design, review, or implement BitPro route icons, action icons, KPI icons, status icons, badges, module marks, strategy icons, SVG assets, or icon design-system rules.
---

# BitPro Icon Design System

## Core Rules

1. Prefer `lucide-react` for product UI icons: actions, navigation, filters, status, tables, KPI headers, empty states, and modal controls.
2. Create custom SVG only for BitPro brand marks, module marks, strategy family badges, or icon sets that lucide cannot express clearly.
3. Keep icons functional and scannable. Do not use decorative icon art to fill space or make cards feel richer.
4. Match the surrounding page style: dark financial shell, compact controls, restrained color, thin strokes, and predictable alignment.
5. Keep sidebar route icon, page header icon, and page documentation aligned when adding or changing a first-level product page.
6. Use Figma skills for icon-system direction when the user asks for a new icon family, brand/module mark, or visual review before implementation.

## BitPro Defaults

- Inline control icons: `h-3 w-3`, `h-3.5 w-3.5`, or `h-4 w-4`.
- Page or card header icons: `h-4 w-4`, `h-5 w-5`, or rare `h-6 w-6`.
- Icon containers: compact square shells such as `h-8 w-8`, `h-9 w-9`, or `h-10 w-10` with `rounded-lg`/`rounded-xl`, `border-crypto-border`, and subtle tinted background.
- Market gain/loss semantics: use `text-up` and `text-down`; do not hard-code red/green for PnL or trend direction.
- State semantics: blue/cyan for information, amber for warning/attention, red for destructive or critical risk, green only for success/healthy states where it does not conflict with configured market colors.

## Reference

Load `references/icon-contract.md` before substantial icon-family, custom SVG, route identity, brand/module mark, or Figma icon-library work.

## Verification

Before declaring icon work complete:

- Check icons are visible on `bg-crypto-bg` and `bg-crypto-card`.
- Check icons do not shift button, tab, table, or card layout.
- Check labels and tooltips still identify icon-only controls.
- Check route icons match page header identity for sidebar-visible routes.
- Run targeted static checks, `git diff --check`, and browser/screenshot verification when a permitted target exists.
