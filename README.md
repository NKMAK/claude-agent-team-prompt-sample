# Agent Teams 実験リポジトリ

Claude Code CLI の Agent Teams 機能を実験するためのリポジトリです。

## 概要

Agent Teams は、複数の専門エージェントが協調して複雑なタスクに取り組む Claude Code CLI の実験的機能です。
。

## 現在のコンテンツ

### [agent-teams/design-planning](agent-teams/design-planning/)

設計・計画タスク用の4エージェントチームテンプレートです。

- **Requirements Agent**: 要件の聞き取り・明確化
- **Planning Agent**: タスク分解・実装計画作成
- **Architect Agent**: 技術設計・アーキテクチャ提案
- **Reviewer Agent**: 批判的レビュー・品質チェック

詳細は [agent-teams/design-planning/README.md](agent-teams/design-planning/README.md) を参照してください。

課題：https://github.com/NKMAK/claude-agent-team-prompt-sample/issues

## 使い方

各ディレクトリのREADMEに従って、Agent Teams を活用してください。

## 今後の追加予定

- コードレビュー専門チーム
- テスト戦略チーム
- その他の専門チームテンプレート

---

**注意**: Agent Teams は実験的機能です。大規模なタスクではトークン使用量が大きくなる可能性があります。
