# Codex Karpathy Skills

Codex-native adaptation of the behavioral coding guidelines inspired by Andrej Karpathy's observations on common LLM coding failure modes.

This repository packages the guidance as a Codex skill instead of a `CLAUDE.md` file or Claude plugin.

## Included skill

- `karpathy-guidelines`: Use when coding, reviewing, or refactoring to reduce silent assumptions, overengineering, broad edits, and weak success criteria.

## Install

Copy the skill folder into your local Codex skills directory:

```bash
mkdir -p "$CODEX_HOME/skills"
cp -R skills/karpathy-guidelines "$CODEX_HOME/skills/"
```

Or copy it into a repo-local skills location if that is how you organize Codex skills.

## Source

Adapted from [`forrestchang/andrej-karpathy-skills`](https://github.com/forrestchang/andrej-karpathy-skills).

## License

MIT
