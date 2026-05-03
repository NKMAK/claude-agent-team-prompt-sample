# レビューレポート: TODOアプリ

## 総合評価
- **判定**: 条件付き承認
- **重大な懸念**: あり（計画書と設計書の間に複数の不整合が存在する）

## プランレビュー

### タスク粒度
- **良い点**: 全タスクの見積もりが30分〜4時間の範囲に収まっており、適切な粒度である。各タスクに明確な完了条件（チェックリスト）があり、独立して検証可能。
- **良い点**: Task 4.1 と Task 4.2 が並列実行可能な依存関係になっており、効率的な開発順序が設計されている。

### 開発順序
- **良い点**: ボトムアップの開発順序（基盤 -> データモデル -> ビジネスロジック -> UI -> 統合）が適切に守られている。
- **良い点**: 依存関係図が明確に記載されており、タスク間の関係が把握しやすい。
- **良い点**: 各フェーズ完了時の検証ポイントが具体的に定義されている。

## 設計レビュー

### YAGNI
- **良い点**: architecture-doc に「YAGNI確認 - 排除した機能」セクションがあり、フィルタリング、編集、ドラッグ&ドロップ、Redux/Zustand、Context API を明示的に除外している。非常に良い判断。
- **懸念点**: plan-doc の Task 2.1 では Todo 型に `createdAt` フィールドを含めているが、architecture-doc の型定義には含まれていない。要件ドキュメントにはソート機能や作成日時の表示に関する要件がないため、`createdAt` は YAGNI の可能性がある。

### 問題: plan-doc の `createdAt` フィールドが YAGNI の疑い
- **現状**: plan-doc Task 2.1 で `Todo` 型に `createdAt` フィールドを含めている
- **懸念**: 要件ドキュメントに作成日時の表示やソートの要件がない。architecture-doc の型定義にも含まれていない。現時点で不要なフィールドである可能性が高い
- **推奨**: `createdAt` を削除し、`Todo { id: string; title: string; completed: boolean }` とする（architecture-doc に合わせる）
- **影響**: 型定義がシンプルになり、plan-doc と architecture-doc の整合性が取れる

対応しますか？
- はい、修正する
- いいえ、現状維持（理由があれば）
- 別の方法で対応する

### 過度な抽象化
- **良い点**: BaseClass や AbstractService のような不要な抽象化が一切ない。
- **良い点**: 状態管理に useState + カスタムフック（`useTodos`）というシンプルなパターンを採用しており、React の標準的なアプローチに沿っている。
- **良い点**: ストレージ層（localStorage操作）、ロジック層（カスタムフック）、UI層（コンポーネント）の3層分離は適切で、過度ではない。

### 1ファイル1責任
- **良い点**: 各ファイルの責任が明確に定義されている（TodoForm=入力、TodoItem=個別表示、TodoList=一覧表示、useTodos=CRUDロジック、todoStorage=永続化）。
- **良い点**: このアプリの規模では、各ファイルが200-300行を超える可能性は低い。

### 保守性 / セキュリティ / パフォーマンス
- **良い点**: localStorage のエラーハンドリング（JSONパースエラー時のフォールバック）が plan-doc に明記されている。
- **良い点**: props のバケツリレーが浅い（App -> TodoList -> TodoItem の2段のみ）ため、Context API なしで十分。
- **懸念点**: plan-doc のリスク管理に「React の状態管理と localStorage 同期のタイミングずれ」が Medium リスクとして挙げられているが、具体的な実装方針（useEffect での同期）が plan-doc 側のリスク軽減策に記載されているのみで、architecture-doc の useTodos 定義にはその同期方法の詳細が記載されていない。実装時に迷わないよう、architecture-doc にも補足があるとよい。

## 重大な問題: plan-doc と architecture-doc の不整合

計画書と設計書の間に以下の不整合が存在する。実装時にどちらに従うべきか混乱を招くため、実装前に統一する必要がある。

### 問題: ファイルパスの不一致
- **現状**:
  | 項目 | plan-doc | architecture-doc |
  |------|----------|------------------|
  | 型定義 | `src/types/todo.ts` | `src/types.ts` |
  | ストレージ | `src/utils/storage.ts` | `src/storage/todoStorage.ts` |
  | 入力コンポーネント | `src/components/TodoInput.tsx` | `src/components/TodoForm.tsx` |
  | CSSファイル | 各コンポーネントに `.css` ファイルあり | 記載なし |
- **懸念**: 実装者がどちらのパスに従うべきか判断できない。2つのドキュメントが矛盾していると、手戻りの原因になる
- **推奨**: architecture-doc のファイル構成を正とし、plan-doc の影響ファイル欄を architecture-doc に合わせて修正する。CSSファイルについては architecture-doc に追記するか、CSS-in-JS やCSS Modules の採用を明記する
- **影響**: ドキュメントの整合性が取れ、実装時の混乱を防止できる

対応しますか？
- はい、修正する
- いいえ、現状維持（理由があれば）
- 別の方法で対応する

### 問題: Todo 型定義の不一致
- **現状**: plan-doc では `id, title, completed, createdAt` の4フィールド、architecture-doc では `id, title, completed` の3フィールド
- **懸念**: 型定義はアプリ全体に影響するため、不一致は早期に解決すべき
- **推奨**: 上記 YAGNI の項で述べた通り、`createdAt` を除外して architecture-doc に合わせる
- **影響**: 型定義が統一され、実装時の混乱を防止できる

対応しますか？
- はい、修正する
- いいえ、現状維持（理由があれば）
- 別の方法で対応する

## 推奨アクション

### 高優先度（実装前に必須）
1. **plan-doc と architecture-doc のファイルパスを統一する** → ユーザー確認: はい/いいえ/別の方法
2. **Todo 型の `createdAt` フィールドについて方針を決定する** → ユーザー確認: はい/いいえ/別の方法

### 中優先度（実装中に対応）
1. architecture-doc の `useTodos` 定義に、localStorage との同期方法（useEffect による状態変更検知と保存）を補足する
2. スタイリング方針（素のCSS / CSS Modules / その他）を architecture-doc に明記する

### 低優先度（余裕があれば）
1. テスト戦略について、実施するかどうかを明確にする（実施する場合は Task 1.1 にテストフレームワーク導入を追加）
