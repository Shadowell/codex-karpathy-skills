# Agent Skills

Reusable agent skills for Codex, Claude, OpenCode, DeepSeek, and other coding agents that can read `SKILL.md`-style instruction bundles.

The repository started as a Codex-native adaptation of the behavioral coding guidelines inspired by Andrej Karpathy's observations on common LLM coding failure modes. It now also includes BitPro project skills copied from the BitPro project-level `.agents/skills` directory.

## Included Skills

- `karpathy-guidelines`: Use when coding, reviewing, or refactoring to reduce silent assumptions, overengineering, broad edits, and weak success criteria.
- `bitpro-agent-interface`: Use when an external Agent needs BitPro MCP/API tool calling, interface selection, parameter clarification, stable tool contracts, or live-trading risk boundaries.
- `bitpro-strategy-research`: Use when researching, writing, validating, backtesting, paper-running, or live-trading BitPro strategies through BitPro MCP tools.
- `bitpro-docs-maintainer`: Use when updating BitPro README, product specifications, progress records, contracts, screenshots, disclaimers, or operational docs.
- `bitpro-frontend-design-system`: Use when designing, polishing, implementing, or verifying BitPro React/Tailwind operator-facing UI.
- `bitpro-icon-design-system`: Use when selecting, designing, reviewing, or implementing BitPro route icons, action icons, badges, SVG marks, or icon-system rules.

## Install For Codex

Copy one skill or the whole skill set into your local Codex skills directory:

```bash
mkdir -p "$CODEX_HOME/skills"
cp -R skills/karpathy-guidelines "$CODEX_HOME/skills/"
cp -R skills/bitpro-* "$CODEX_HOME/skills/"
```

Or copy the folders into a repo-local skills location, such as `.agents/skills/`, when you want the skills to travel with a specific project.

## Use With Other Agents

Claude, OpenCode, DeepSeek, and other agents can use the same content by reading the relevant `skills/<name>/SKILL.md` file before work starts. If the agent supports bundled resources, keep each skill folder intact so `agents/`, `references/`, `scripts/`, or `assets/` remain available.

Recommended prompts:

```text
Use skills/bitpro-agent-interface/SKILL.md before calling BitPro MCP/API tools.
```

```text
Use skills/bitpro-strategy-research/SKILL.md before creating, updating, backtesting, or paper-running a BitPro strategy.
```

```text
Use skills/bitpro-frontend-design-system/SKILL.md and skills/bitpro-icon-design-system/SKILL.md before changing BitPro UI or icons.
```

## Source

Adapted from [`forrestchang/andrej-karpathy-skills`](https://github.com/forrestchang/andrej-karpathy-skills).

BitPro skills are copied from the project-level skills in `Shadowell/BitPro`.

## License

MIT
