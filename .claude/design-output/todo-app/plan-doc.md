# 実装プラン: TODOアプリ

## 概要
- **開発順序の方針**: ボトムアップ（下層から上層）
- **主なリスク**: localStorage の容量制限やブラウザ互換性に関する軽微なリスクのみ。全体としてシンプルなスコープのため低リスク。

## フェーズ別タスク

### Phase 1: プロジェクト基盤セットアップ
**目的**: 開発環境を構築し、全タスクの土台を整える

#### Task 1.1: React + TypeScript プロジェクトを初期化する
- **依存**: なし
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] Vite + React + TypeScript でプロジェクトが作成されている
  - [ ] `npm run dev` で開発サーバーが起動し、ブラウザで初期画面が表示される
  - [ ] 不要なボイラープレートファイルが削除されている
  - [ ] ディレクトリ構造（`src/types/`, `src/hooks/`, `src/components/`, `src/utils/`）が作成されている
- **影響ファイル**:
  - 新規: `package.json`, `tsconfig.json`, `vite.config.ts`, `index.html`
  - 新規: `src/main.tsx`, `src/App.tsx`, `src/App.css`

---

### Phase 2: データモデル・ユーティリティ層
**目的**: タスクの型定義と永続化ロジックを実装し、上位層の基盤を作る

#### Task 2.1: `src/types/todo.ts` に Todo 型を定義する
- **依存**: Task 1.1
- **優先度**: High
- **見積もり**: 30分 ~ 1時間
- **完了条件**:
  - [ ] `Todo` 型が `id`, `title`, `completed`, `createdAt` フィールドを持つ
  - [ ] 型が正しくエクスポートされ、他ファイルからインポート可能である
  - [ ] TypeScript のコンパイルエラーがない
- **影響ファイル**:
  - 新規: `src/types/todo.ts`

#### Task 2.2: `src/utils/storage.ts` に localStorage 操作関数を実装する
- **依存**: Task 2.1
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] `loadTodos(): Todo[]` 関数が localStorage からタスク一覧を読み込む
  - [ ] `saveTodos(todos: Todo[]): void` 関数がタスク一覧を localStorage に保存する
  - [ ] localStorage が空の場合、空配列を返す
  - [ ] JSON パースエラー時に空配列にフォールバックする
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 新規: `src/utils/storage.ts`

---

### Phase 3: ビジネスロジック層
**目的**: Todoの追加・完了切替・削除ロジックをカスタムフックとして実装し、UIから独立してテスト可能にする

#### Task 3.1: `src/hooks/useTodos.ts` にカスタムフックを実装する
- **依存**: Task 2.1, Task 2.2
- **優先度**: High
- **見積もり**: 2時間 ~ 4時間
- **完了条件**:
  - [ ] `useTodos()` フックが `todos`, `addTodo`, `toggleTodo`, `deleteTodo` を返す
  - [ ] `addTodo(title: string)` で新しいタスクが追加される（FR-1）
  - [ ] 空文字のタイトルでは追加されない（FR-1 受け入れ基準）
  - [ ] `toggleTodo(id: string)` で完了/未完了が切り替わる（FR-3）
  - [ ] `deleteTodo(id: string)` でタスクが削除される（FR-4）
  - [ ] 状態変更が localStorage に自動保存される
  - [ ] 初回マウント時に localStorage からデータが読み込まれる
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 新規: `src/hooks/useTodos.ts`

---

### Phase 4: UIコンポーネント層
**目的**: 個別のUIコンポーネントを実装し、ユーザー操作を受け付けられるようにする

#### Task 4.1: `src/components/TodoInput.tsx` にタスク入力コンポーネントを実装する
- **依存**: Task 3.1
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] テキスト入力欄と追加ボタンが表示される
  - [ ] Enterキーまたはボタンクリックで `addTodo` が呼ばれる（FR-1）
  - [ ] 空文字入力時は追加されない（FR-1 受け入れ基準）
  - [ ] 追加後に入力欄がクリアされる（FR-1 受け入れ基準）
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 新規: `src/components/TodoInput.tsx`
  - 新規: `src/components/TodoInput.css`

#### Task 4.2: `src/components/TodoItem.tsx` にタスク表示コンポーネントを実装する
- **依存**: Task 3.1
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] タスク名、チェックボックス、削除ボタンが表示される（FR-2）
  - [ ] 完了タスクに取り消し線が適用される（FR-2 受け入れ基準）
  - [ ] チェックボックスクリックで `toggleTodo` が呼ばれる（FR-3）
  - [ ] 削除ボタンクリックで `deleteTodo` が呼ばれる（FR-4）
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 新規: `src/components/TodoItem.tsx`
  - 新規: `src/components/TodoItem.css`

#### Task 4.3: `src/components/TodoList.tsx` にタスク一覧コンポーネントを実装する
- **依存**: Task 4.2
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] `Todo[]` を受け取り、`TodoItem` をリスト表示する（FR-2）
  - [ ] タスクが0件の場合、空状態メッセージを表示する
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 新規: `src/components/TodoList.tsx`
  - 新規: `src/components/TodoList.css`

---

### Phase 5: ページ統合・スタイリング
**目的**: 全コンポーネントを統合し、レスポンシブ対応のアプリとして完成させる

#### Task 5.1: `src/App.tsx` に全コンポーネントを統合する
- **依存**: Task 4.1, Task 4.3
- **優先度**: High
- **見積もり**: 1時間 ~ 2時間
- **完了条件**:
  - [ ] `useTodos` フックを利用し、`TodoInput` と `TodoList` が連携動作する
  - [ ] タスク追加 → 一覧表示 → 完了切替 → 削除 の一連操作が正常に動作する
  - [ ] ページリロード後もデータが保持される（localStorage 永続化）
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 修正: `src/App.tsx`
  - 修正: `src/App.css`

#### Task 5.2: レスポンシブ対応とスタイリングを適用する
- **依存**: Task 5.1
- **優先度**: Medium
- **見積もり**: 1時間 ~ 3時間
- **完了条件**:
  - [ ] モバイル（375px以上）でレイアウトが崩れない
  - [ ] デスクトップでも適切な幅で表示される（最大幅の設定）
  - [ ] 操作後100ms以内にUIが更新される（非機能要件）
  - [ ] 視覚的に見やすく、操作しやすいデザインである
  - [ ] 動作検証が完了している
- **影響ファイル**:
  - 修正: `src/App.css`
  - 修正: `src/components/TodoInput.css`
  - 修正: `src/components/TodoItem.css`
  - 修正: `src/components/TodoList.css`

---

## 依存関係図

```
Phase 1 (基盤セットアップ)
  └─→ Phase 2 (データモデル・ユーティリティ)
        └─→ Phase 3 (ビジネスロジック)
              └─→ Phase 4 (UIコンポーネント)
                    └─→ Phase 5 (統合・スタイリング)
```

```
Task 1.1
  └─→ Task 2.1
        └─→ Task 2.2
              └─→ Task 3.1
                    ├─→ Task 4.1 ─────→ Task 5.1
                    └─→ Task 4.2                │
                          └─→ Task 4.3 ─→ Task 5.1
                                              └─→ Task 5.2
```

## 検証ポイント

- **Task 2.2 完了時**: localStorage への保存・読み込みが正しく動作すること。ブラウザのDevToolsでlocalStorageの値を確認する。
- **Task 3.1 完了時**: React DevTools またはコンソールから `useTodos` の各関数を呼び出し、状態が正しく更新されること。
- **Task 4.2 完了時**: 個別の TodoItem が正しくレンダリングされ、完了状態の取り消し線が表示されること。
- **Task 5.1 完了時**: タスク追加 → 一覧表示 → 完了切替 → 削除 の一連操作が正常に動作すること。ページリロード後もデータが保持されること。
- **Task 5.2 完了時**: Chrome DevTools のデバイスモードで375px幅にし、レイアウトが崩れないこと。

## リスク管理

| リスク | 影響 | 軽減策 |
|-------|------|-------|
| localStorage の容量制限（約5MB） | Low | TODOアプリのデータ量では到達しない。将来的に必要であれば IndexedDB への移行を検討 |
| ブラウザの localStorage 無効設定 | Low | storage.ts のエラーハンドリングでフォールバック（空配列を返す）を実装 |
| React の状態管理とlocalStorage同期のタイミングずれ | Medium | useEffect で状態変更を検知し、即座に localStorage へ保存する設計にする |
