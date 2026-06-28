Language: [English](README.md) | 日本語

# search-first

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/search-first)

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

0. **言語化（必須・text 出力）** — ツール呼び出しの前に、要件・言語/framework・制約を 2-3 文で外化する。subsequent tool args への埋め込みは Step 0 を満たさない
1. **ニーズ分析** — 必要な機能を定義する
2. **並列検索** — npm/PyPI、MCP サーバー、GitHub、既存スキルを一斉に検索する
3. **評価** — 機能性、メンテナンス状況、コミュニティ、ドキュメント、ライセンス、依存関係でスコアリングする
4. **判断** — そのまま採用、拡張/ラップ、複数パッケージの組み合わせ、またはカスタム実装を決定する
5. **実装** — 選択したソリューションを最小限のカスタムコードで導入する

ユーザーが「研究するな」と言った場合でも、silent skip ではなく **1 段落で trade-off を明示** してから実装に入る（ユーザーが代替ライブラリの存在を知らない場合のための course-correct ウィンドウ）。

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
ニーズ: Markdown ファイルのリンク切れをチェックしたい
検索: npm "markdown dead link checker"
発見: textlint-rule-no-dead-link（スコア: 9/10）
アクション: ADOPT — npm install textlint-rule-no-dead-link
結果: カスタムコードゼロ、実績のあるソリューションを採用
```

## このスキルについて

このスキルは [Agent Knowledge Cycle (AKC)](https://github.com/shimo4228/agent-knowledge-cycle) の **Research** フェーズを実装する — エージェント行動とオペレーターの判断が共発展する 6 フェーズ双方向成長ループ ([DOI 10.5281/zenodo.19200726](https://doi.org/10.5281/zenodo.19200726))。AKC は [@shimo4228](https://github.com/shimo4228) の 3 つの研究ラインの 1 つで、他に [Contemplative Agent](https://github.com/shimo4228/contemplative-agent) ([DOI 10.5281/zenodo.19212118](https://doi.org/10.5281/zenodo.19212118)) — 4 つの contemplative 公理に基づく自律エージェント — と [Agent Attribution Practice (AAP)](https://github.com/shimo4228/agent-attribution-practice) ([DOI 10.5281/zenodo.19652013](https://doi.org/10.5281/zenodo.19652013)) — 自律 AI エージェントの責任分配に関するハーネス中立 ADR — がある。

## ライセンス

MIT
