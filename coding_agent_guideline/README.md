# Coding Agent Guidelines

A practical guide for Wang Lab members working with coding agents (Claude Code, Codex) on HiPerGator.

- [How to Use a Coding Agent](./how_to_use_coding_agent.md)

---

## Quick Start

- Install Claude Code: `curl -fsSL https://claude.ai/install.sh | bash`
- Keep a session alive across SSH drops: `module load tmux/3.5` then `tmux`
- Start the agent: `claude`, then `/login` on first use
- Give the agent project context in `CLAUDE.md`; set what it may run in `.claude/settings.json`

---

> Heavy work belongs on a compute node, not the login node. Have the agent use `srun`/`sbatch`. See [Server Guideline](../server_guideline/README.md).

Written by Dongyuan Li. Suggestions and PRs welcome.
