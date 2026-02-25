# アーキテクチャ設計: TODOアプリ

## 概要

React + TypeScript によるシンプルなTODOアプリ。フロントエンドのみで動作し、localStorage でデータを永続化する。コンポーネントは機能単位で分割し、状態管理は React の useState で行う。

## ファイル構成

### 新規作成

```
src/
├── main.tsx              # エントリーポイント（ReactDOM.render）
├── App.tsx               # アプリ全体のレイアウトと状態管理
├── types.ts              # Todo の型定義
├── components/
│   ├── TodoForm.tsx      # タスク入力フォーム（FR-1）
│   ├── TodoList.tsx      # タスク一覧表示（FR-2）
│   └── TodoItem.tsx      # 個別タスク表示・操作（FR-3, FR-4）
├── hooks/
│   └── useTodos.ts       # Todo の CRUD ロジック + localStorage 永続化
└── storage/
    └── todoStorage.ts    # localStorage の読み書き
```

### 修正するファイル

- なし（新規プロジェクト）

## 各ファイルの機能定義

### `src/types.ts`
**責任**: Todo データの型定義
**型**:
- `Todo { id: string; title: string; completed: boolean }`

### `src/storage/todoStorage.ts`
**責任**: localStorage への Todo データの読み書き
**関数**:
- `loadTodos(): Todo[]` - localStorage から Todo 一覧を読み込む
- `saveTodos(todos: Todo[]): void` - localStorage に Todo 一覧を保存する

### `src/hooks/useTodos.ts`
**責任**: Todo の状態管理と CRUD 操作を提供するカスタムフック
**関数**:
- `useTodos(): { todos: Todo[]; addTodo: (title: string) => void; toggleTodo: (id: string) => void; deleteTodo: (id: string) => void }`
  - 初期化時に `loadTodos()` で localStorage から読み込む
  - 状態変更時に `saveTodos()` で localStorage に保存する

### `src/components/TodoForm.tsx`
**責任**: タスク入力フォームの UI と入力バリデーション
**Props**:
- `onAdd: (title: string) => void`
**動作**:
- テキスト入力欄と追加ボタンを表示
- Enter キーまたはボタンクリックで `onAdd` を呼ぶ
- 空文字の場合は追加しない
- 追加後に入力欄をクリアする

### `src/components/TodoItem.tsx`
**責任**: 個別タスクの表示と操作（完了切替・削除）
**Props**:
- `todo: Todo`
- `onToggle: (id: string) => void`
- `onDelete: (id: string) => void`
**動作**:
- チェックボックス、タスク名、削除ボタンを表示
- 完了タスクはタスク名に取り消し線を適用
- チェックボックスクリックで `onToggle` を呼ぶ
- 削除ボタンクリックで `onDelete` を呼ぶ

### `src/components/TodoList.tsx`
**責任**: タスク一覧の表示
**Props**:
- `todos: Todo[]`
- `onToggle: (id: string) => void`
- `onDelete: (id: string) => void`
**動作**:
- `todos` を `TodoItem` のリストとして表示する
- タスクが 0 件の場合は空状態メッセージを表示する

### `src/App.tsx`
**責任**: アプリ全体のレイアウトと状態管理の接続
**動作**:
- `useTodos` フックを呼び出し、状態と操作関数を取得
- `TodoForm` と `TodoList` を配置し、props を渡す

### `src/main.tsx`
**責任**: React アプリのマウント
**動作**:
- `App` を DOM にレンダリングする

## ファイル間の依存関係

```
main.tsx
  └── App.tsx
        ├── useTodos.ts (hook)
        │     ├── types.ts
        │     └── todoStorage.ts
        │           └── types.ts
        ├── TodoForm.tsx
        └── TodoList.tsx
              └── TodoItem.tsx
                    └── types.ts
```

**データフロー**:
1. `App` が `useTodos` から todos 配列と操作関数を取得
2. `TodoForm` がユーザー入力を受け取り、`addTodo` を呼ぶ
3. `TodoList` / `TodoItem` が todos を表示し、`toggleTodo` / `deleteTodo` を呼ぶ
4. `useTodos` 内で状態を更新し、`todoStorage` で localStorage に同期する

## API設計

バックエンド不要のため、API は存在しない。データは localStorage で管理する。

**localStorage キー**: `"todos"`
**データ形式**: `Todo[]` の JSON 文字列

## 設計の根拠

- **なぜこのファイル構成か**: コンポーネント（UI）、フック（ロジック）、ストレージ（永続化）の3層に分離することで、各ファイルの責任が明確になる。UI の変更がロジックに影響せず、ストレージの変更がUIに影響しない。
- **なぜカスタムフックか**: `useTodos` に CRUD ロジックと localStorage 同期をまとめることで、`App.tsx` がシンプルになる。React の標準パターンであり、特別な状態管理ライブラリは不要。
- **なぜ TodoList と TodoItem を分離するか**: TodoItem は 1 つのタスクの表示・操作に責任を持ち、TodoList はリスト全体の表示に責任を持つ。1 ファイル 1 責任の原則に従う。
- **YAGNI 確認 - 排除した機能**:
  - フィルタリング機能（全件/未完了/完了の切替）は要件にないため含めない
  - タスクの編集機能は要件にないため含めない
  - ドラッグ&ドロップによる並び替えは含めない
  - カテゴリやタグ機能は含めない
  - Redux / Zustand などの状態管理ライブラリは不要（useState で十分）
  - Context API も不要（props のバケツリレーが浅い）

## テスト戦略

- **単体テスト**:
  - `todoStorage.ts`: localStorage への読み書きが正しく行われるか
  - `useTodos.ts`: addTodo / toggleTodo / deleteTodo が状態を正しく更新するか
- **コンポーネントテスト**:
  - `TodoForm`: 入力・送信・バリデーション・クリアの動作
  - `TodoItem`: 完了切替・削除ボタンのクリックイベント、取り消し線の表示
  - `TodoList`: リスト表示、空状態メッセージの表示
- **統合テスト**:
  - `App`: タスクの追加 -> 表示 -> 完了切替 -> 削除の一連のフロー
