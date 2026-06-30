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

**Discipline, not mechanism.** The *how* of searching — parallel subagents, live
doc lookup, registry queries — changes with your agent harness. This skill pins
down the part that doesn't:

1. **Articulate first (Step 0)** — before any tool call, state in plain text what
   functionality is needed, the language/framework, and any constraints. This
   externalizes the query so you can be redirected early, and leaves an auditable record.
2. **Search by source priority** — this repo first (`rg`), then package registries
   (npm / PyPI / …), configured MCP servers, installed skills/tools, and finally
   maintained OSS / templates. Query live docs at decision time rather than trusting
   remembered package facts.
3. **Decide, and record a verdict** — assess candidates holistically (functional fit,
   maintenance, community, docs, license, deps) in prose. **No numeric scores or
   grades** — they manufacture false precision. End with one recognizable line:
   `Verdict: <Adopt|Extend|Compose|Build> — <package(s) or "custom"> — <evidence-based reason>`

For non-trivial needs, delegate the search sweep to whatever research subagent your
harness provides (Full Mode); run it inline for a single obvious need (Quick Mode).
When the user says "don't research", skipping is itself a decision — record it as a
verdict line (`chosen without research at your request`) instead of silently
complying, so they can still course-correct before you build.

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

## Example

```
Need: Check markdown files for broken links (Node project, MIT-compatible only)
Search: this repo → npm "markdown dead link checker"
Found: textlint-rule-no-dead-link — active maintenance, MIT, covers all link types
Verdict: Adopt — textlint-rule-no-dead-link — active, MIT, full coverage
Result: zero custom code, battle-tested solution
```

The verdict is recorded as a single line — evidence, not a score. A pass that
searches but records no verdict line is incomplete.

## About this skill

This skill implements the **Research** phase of the [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) — a Zenodo-citable six-phase bidirectional growth loop ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726)) for sustaining intent alignment between an AI agent and its operator over time. AKC is one of three research lines by [@shimo4228](https://github.com/shimo4228), alongside [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — autonomous agents grounded in four contemplative axioms — and [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)) — harness-neutral ADRs on accountability distribution.

## License

MIT