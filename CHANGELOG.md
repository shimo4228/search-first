# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed
- Repository renamed to drop the `claude-skill-` prefix. This skill follows the
  open Agent Skills standard and is not Claude-specific; the old name implied
  otherwise. The previous URL redirects to the new one.
- Added a `compatibility` frontmatter field (per the Agent Skills spec).
- Updated sibling cross-references to the renamed sibling repositories.

## [1.0.1] — 2026-05-20

Doc-only release. **`skills/search-first/SKILL.md` is unchanged from v1.0.0** — this release documents a paired host-rule pattern that materially improved measured compliance, and updates the baseline numbers accordingly.

### Changed (documentation only)

- **Updated compliance baseline** measured via [skill-comply](https://github.com/shimo4228/skill-comply) (same SKILL.md, paired with the harness-rule snippet below):

| Scenario | v1.0.0 baseline | v1.0.1 baseline (paired rule) | Δ |
|---|---|---|---|
| supportive | 86% | **100%** | +14pp |
| neutral | 29% | **75%** | **+46pp** |
| competing | 57% | 50% | -7pp (within noise) |
| **Overall** | **57%** | **75%** | **+18pp** |

### Recommended paired harness rule

The compliance lift comes from making `/search-first` the **enforced entry point** of an external-research phase, instead of letting the agent invoke `scout` or `WebSearch` directly. Add this snippet to your harness's planning / research rule (e.g. `~/.claude/rules/common/planning.md`):

> ## Phase 0: External Research (Plan Mode)
>
> 計画のステップを書き出す**前に**、既存ソリューションを調査する。
>
> **エントリポイントは `/search-first` skill の呼び出しに固定する**。WebSearch / scout を直接呼んで Phase 0 を済ませてはいけない — skill body が定める Step 0 (要件の text 出力) を skip すると、ユーザーに対する course-correct ウィンドウが閉じ、後段の Decide / Implement に必要な articulation が記録に残らない。scout は search-first の **Full Mode 内部** で起動される実装詳細であり、Phase 0 と等価ではない。

Without this paired rule the v1.0.0 baseline (57% overall) is what you should expect. With it, the v1.0.1 baseline (75% overall) is achievable on the same skill body.

### Root cause analysis

The neutral-scenario gap (29% in v1.0.0 baseline) was traced to vocabulary duplication: many harnesses already define a "Phase 0 External Research" concept in their planning rule, and the agent reads that as license to invoke `scout` or `WebSearch` directly — bypassing `/search-first` and therefore skipping the skill's mandatory Step 0 (user-visible articulation). Clarifying the hierarchy (`Phase 0` rule → `/search-first` skill → `scout` implementation) routes invocations through the skill and recovers the Step 0 enforcement.

## [1.0.0] — 2026-05-20

Initial public release.

### Added

- **Skill body** (`skills/search-first/SKILL.md`)
  - 6-step workflow: **Step 0 Articulate** (mandatory user-visible text output before any tool call) → 1. Need Analysis → 2. Parallel Search → 3. Evaluate → 4. Decide → 5. Implement
  - 4-way decision matrix: **Adopt** (exact match, well-maintained, MIT/Apache) / **Extend** (partial match, thin wrapper) / **Compose** (multiple small packages) / **Build** (informed by research)
  - Parallel search targets: npm registry, PyPI, MCP servers, GitHub, existing skills
  - Quick Mode (inline checklist) and Full Mode (scout subagent)
  - **Skip-research handling**: when the user prompt says "don't research, just implement", the agent emits a one-paragraph trade-off acknowledgement (name the tentative choice + reason + offer 60-second scan) instead of silently complying — preserves user intent while keeping the course-correct window open
  - Articulation **format requirement**: Step 0 output must be user-visible assistant text, not embedded in subsequent tool arguments
- **AI-facing docs**: `llms.txt` (root navigator) + `llms-full.txt` (self-contained 10-Q&A reference) optimized for ChatGPT / Perplexity / Gemini citation
- **Marketplace metadata**: `.claude-plugin/marketplace.json` for `/skills add shimo4228/search-first` install
- **Multi-language READMEs**: English (`README.md`) and Japanese (`README.ja.md`)

### Compliance baseline

Measured via [skill-comply](https://github.com/shimo4228/skill-comply) at release time (3 prompt-strictness scenarios, agent=sonnet):

| Scenario | Compliance | Notes |
|---|---|---|
| supportive | 86% | All steps pass except optional `search_mcp` |
| neutral | 29% | Agent self-initiates only `search_repo`; spec generator splits search into 4 mandatory sub-steps |
| competing | 57% | `articulate_requirement` + `evaluate` + `implement` all pass even under "skip research" pressure |
| **Overall** | **57%** | — |

The load-bearing behavioral metric — **`articulate_requirement` (3/3 across all scenarios)** — is the design target this release was tuned for.

### Iteration history (development phase, pre-release)

Two iterations were applied during 2026-05-20 development to lift `articulate_requirement` from 1/3 to 3/3:

- **Iter 1 (body patch)**: replaced Quick Mode "mentally run through" with explicit `Step 0: Articulate the requirement (mandatory, text output)`. Added "When the user says 'skip research'" section. Supportive: 67% → 100%.
- **Iter 2 (description + format requirement)**: expanded description from 132 to 889 chars with trigger phrase examples (`add X`, `implement Y`, payment/E2E/linter/CI domains) and explicit DO-NOT cases. Embedded `planning.md Phase 0 External Research` as vocabulary bridge. Added Step 0 body note that articulation must be user-visible text, not tool-args embedding. Articulate compliance: 1/3 → 3/3 across all scenarios.

### Related skills

- [skill-comply](https://github.com/shimo4228/skill-comply) — used to measure compliance baseline above
- [llms-txt-writer](https://github.com/shimo4228/llms-txt-writer) — generated this repo's `llms.txt` / `llms-full.txt`

[1.0.0]: https://github.com/shimo4228/search-first/releases/tag/v1.0.0
