# Agent Team 作成プロンプト

以下のプロンプトを Claude Code CLI で実行すると、4つの専門エージェントからなるチームが作成されます。

---

ソフトウェア開発のための4つの専門エージェントからなるチームを作成してください。

各エージェントの詳細な役割定義は以下のファイルを参照してください：
- Requirements Agent: `.claude/agent-teams/design-planning/agent-roles/requirements-agent.md`
- Planning Agent: `.claude/agent-teams/design-planning/agent-roles/planning-agent.md`
- Architect Agent: `.claude/agent-teams/design-planning/agent-roles/architect-agent.md`
- Reviewer Agent: `.claude/agent-teams/design-planning/agent-roles/reviewer-agent.md`

**リーダー（Claude Code）の責任と進行手順：**

1. **フェーズ1: 要件定義**
   - Requirements Agent を起動し、ユーザーへのヒアリングを実施させる
   - Requirements Agent が `requirement-doc.md` を完成させるまで待機する
   - Requirements Agent は **ユーザーから提供された情報のみ** を要件に記載すること（自ら補完・推測しない）

2. **フェーズ2: 設計・計画（並列）**
   - Requirements Agent の完了後、Planning Agent と Architect Agent を **並列で** 起動する
   - 両エージェントは作業中に疑問が生じた場合、Requirements Agent 経由でユーザーに確認する
   - Requirements Agent はこのフェーズ中も待機し、質問対応を継続する
   - リーダーは Planning Agent と Architect Agent の **両方が完了するまで待機する**

3. **フェーズ3: レビュー**
   - Planning Agent と Architect Agent の **両方が完了した後に** Reviewer Agent を起動する
   - Reviewer Agent がレビュー結果を `review-doc.md` に出力する
   - レビュー完了後、Requirements Agent がレビュー内容をユーザーに報告し、対応方針をAskUserQuestionで確認する
   - **ユーザーの確認が完了するまで、他のエージェントは次のアクションを取らない**

**エージェント間の協調ルール：**
- 各エージェントは独立して作業しつつ、必要に応じて他のエージェントに質問や確認を行う
- 不明点は必ず Requirements Agent 経由でユーザーに確認する（AIの判断で補完しない）
- エージェント間で議論が発生した場合、合意が得られるまで対話を続ける
- すべてのエージェントは `.claude/agent-teams/design-planning/output-templates/` のテンプレートを参照する

**最終成果物：**
- `requirement-doc.md` - 要件ドキュメント
- `plan-doc.md` - 実装計画
- `architecture-doc.md` - アーキテクチャ設計
- `review-doc.md` - レビューレポート

---

## 使用方法

1. `.claude/settings.json` に `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` が設定されていることを確認
2. 上記のエージェントチーム作成プロンプトをコピー
3. Claude Code CLI で実行
4. チームがタスクに協力して取り組み始めます
5. 成果物（`requirement-doc.md`, `plan-doc.md`, `architecture-doc.md`, `review-doc.md`）を確認

## 注意事項

- これは実験的機能です - セッションは再開できません
- 4つのエージェントが動作するため、トークン使用量が多くなります
- 複雑な設計タスクに最適で、単純な実装には不向きです
- 各エージェントは提供されたテンプレートに従ってドキュメントを作成します
- すべてのエージェントはMarkdownドキュメントのみ作成可能で、ソースコードは変更できません
- エージェント間の対話と議論を通じて、より質の高い設計が生まれます
