Language: English | [日本語](README.ja.md)

# search-first

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/search-first) [![GitMCP](https://img.shields.io/endpoint?url=https://gitmcp.io/badge/shimo4228/search-first)](https://gitmcp.io/shimo4228/search-first)

An [Agent Skill](https://agentskills.io/specification) that enforces a **research-before-coding** workflow. Before writing custom code, the agent searches for existing tools, libraries, MCP servers, and patterns — then makes an informed adopt/extend/build decision.

## Install

### Claude Code

```bash
# Copy into your global skills directory
cp -r skills/search-first ~/.claude/skills/search-first
```

### SkillsMP

```bash
/skills add shimo4228/search-first
```

## How It Works

1. **Need Analysis** — Define what functionality is needed
2. **Parallel Search** — Search npm/PyPI, MCP servers, GitHub, and existing skills
3. **Evaluate** — Score candidates on functionality, maintenance, community, docs, license, and deps
4. **Decide** — Adopt as-is, extend/wrap, compose multiple packages, or build custom
5. **Implement** — Install the chosen solution with minimal custom code

## When It Triggers

- Starting a new feature that likely has existing solutions
- Adding a dependency or integration
- Before creating a new utility, helper, or abstraction

## Decision Matrix

| Signal | Action |
|--------|--------|
| Exact match, well-maintained, MIT/Apache | **Adopt** — install and use directly |
| Partial match, good foundation | **Extend** — install + write thin wrapper |
| Multiple weak matches | **Compose** — combine 2-3 small packages |
| Nothing suitable found | **Build** — write custom, but informed by research |

## Examples

```
Need: Check markdown files for broken links
Search: npm "markdown dead link checker"
Found: textlint-rule-no-dead-link (score: 9/10)
Action: ADOPT — npm install textlint-rule-no-dead-link
Result: Zero custom code, battle-tested solution
```

## About this skill

This skill implements the **Research** phase of the [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) — a Zenodo-citable six-phase bidirectional growth loop ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726)) for sustaining intent alignment between an AI agent and its operator over time. AKC is one of three research lines by [@shimo4228](https://github.com/shimo4228), alongside [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — autonomous agents grounded in four contemplative axioms — and [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)) — harness-neutral ADRs on accountability distribution.

## License

MIT