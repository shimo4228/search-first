Language: [English](README.md) | 日本語

# search-first

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/search-first) [![GitMCP](https://img.shields.io/endpoint?url=https://gitmcp.io/badge/shimo4228/search-first)](https://gitmcp.io/shimo4228/search-first)

**リサーチ・ファースト**のワークフローを強制する [Agent Skill](https://agentskills.io/specification) です。カスタムコードを書く前に、既存のツール、ライブラリ、MCP サーバー、パターンを検索し、情報に基づいた Adopt（採用）/ Extend（拡張）/ Build（自作）の判断を下します。

## インストール

### Claude Code

```bash
# グローバル skills ディレクトリにコピー
cp -r skills/search-first ~/.claude/skills/search-first
```

### SkillsMP

```bash
/skills add shimo4228/search-first
```

## 仕組み

**機構ではなく規律。** 検索の *やり方*（並列サブエージェント・ライブ doc 参照・レジストリ照会）はハーネスによって変わる。このスキルが固定するのは変わらない部分:

1. **まず言語化（Step 0・必須）** — ツール呼び出しの前に、要件・言語/framework・制約を plain text で書く。検索クエリを外化して早期に軌道修正でき、監査可能な記録が残る。tool args への埋め込みは Step 0 を満たさない（ユーザーが読むのは chat text）
2. **ソース優先順位で検索** — まず repo 内（`rg`）、次にパッケージレジストリ（npm / PyPI / …）、設定済み MCP サーバー、インストール済みスキル/ツール、最後にメンテされた OSS / テンプレート。記憶に頼らず**判断時にライブ doc を照会**する
3. **判断し、Verdict を記録** — 機能適合・保守・コミュニティ・docs・ライセンス・依存を **prose で** 総合評価する。**numeric score や grade は付けない**（偽の精度を生む）。1 行の判定で締める:
   `Verdict: <Adopt|Extend|Compose|Build> — <package(s) または "custom"> — <根拠ベースの理由>`

非自明な要件では、検索 sweep をハーネスが提供する**調査用サブエージェント**に委譲する（Full Mode）。単純な要件は inline（Quick Mode）。ユーザーが「研究するな」と言っても、skip 自体が 1 つの決定なので **verdict 行で記録**（`chosen without research at your request`）してから実装に入る（silent skip にせず、代替の存在を知らないユーザーが軌道修正できる窓を残す）。

## トリガー条件

- 既存ソリューションがありそうな新機能の開発を始めるとき
- 依存関係やインテグレーションを追加するとき
- 新しいユーティリティ、ヘルパー、抽象化を作成する前

## 判断マトリクス

| シグナル | アクション |
|---------|-----------|
| 完全一致、メンテナンス良好、MIT/Apache ライセンス | **Adopt（採用）** — そのままインストールして使用 |
| 部分一致、基盤として優秀 | **Extend（拡張）** — インストール + 薄いラッパーを作成 |
| 弱い一致が複数 | **Compose（組合せ）** — 小さなパッケージを 2-3 個組み合わせる |
| 適切なものが見つからない | **Build（自作）** — リサーチ結果を踏まえてカスタム実装 |

## 使用例

```
ニーズ: Markdown のリンク切れをチェックしたい（Node プロジェクト、MIT 互換のみ）
検索: repo 内 → npm "markdown dead link checker"
発見: textlint-rule-no-dead-link — メンテ活発、MIT、全リンク種別をカバー
Verdict: Adopt — textlint-rule-no-dead-link — 活発、MIT、フルカバレッジ
結果: カスタムコードゼロ、実績のあるソリューションを採用
```

判定は 1 行で記録する — スコアではなく証拠で。検索しても Verdict 行を残さない pass は未完了。

## このスキルについて

このスキルは [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) の **Research** フェーズを実装する — エージェント行動とオペレーターの判断が共発展する 6 フェーズ双方向成長ループ ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726))。AKC は [@shimo4228](https://github.com/shimo4228) の 3 つの研究ラインの 1 つで、他に [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — 4 つの contemplative 公理に基づく自律エージェント — と [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)) — 自律 AI エージェントの責任分配に関するハーネス中立 ADR — がある。

## ライセンス

MIT
