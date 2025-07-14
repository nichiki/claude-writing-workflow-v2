# Claude 文章執筆ワークフロー

Claude Codeを使用した文章執筆の自動化ワークフローシステム。インタビューから執筆、品質チェックまでを一貫して管理できます。

## 特徴

- **対話的インタビュー**: 記事の要件を自然な対話で収集
- **自動リサーチ**: Web検索による情報収集
- **アウトライン生成**: 構造化された記事設計
- **スタイルガイド対応**: 一貫した文体での執筆
- **品質チェック**: textlintによる自動校正
- **バッチ処理**: CSVファイルからの一括記事作成

## セットアップ

### 1. このテンプレートから新しいリポジトリを作成

「Use this template」ボタンをクリックして、新しいリポジトリを作成してください。

### 2. リポジトリをローカルにクローン

```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```

### 3. 依存関係のインストール

```bash
npm install
```

### 4. Claude Codeでプロジェクトを開く

```bash
claude
```

## 基本的な使い方

### 新規記事作成（対話式）

```
/new
```

インタビューから執筆まで、すべてのステップを一気通貫で実行します。

### バッチ処理（CSV一括作成）

1. `batch_inputs/example.csv`をコピーして編集
2. 以下のコマンドで実行

```
/batch batch_inputs/your-articles.csv
```

### 個別ステップ実行

```
/step:interview    # インタビューのみ
/step:research     # リサーチのみ
/step:outline      # アウトライン生成のみ
/step:draft        # 執筆のみ
/step:check        # 品質チェックのみ
```

## ディレクトリ構造

```
├── .claude/commands/     # コマンド定義
├── config/               # 設定ファイル
│   ├── brief/           # プリセット設定
│   ├── styles/          # スタイルガイド
│   └── textlint/        # textlint設定
├── docs/                 # ドキュメント
├── batch_inputs/         # バッチ処理用CSVファイル
├── projects/             # 作成された記事プロジェクト
└── package.json
```

## コマンド一覧

### メインワークフロー
- `/new` - 新規記事作成（フルワークフロー）
- `/batch` - CSV一括処理

### 個別ステップ
- `/step:interview` - 対話的インタビュー
- `/step:research` - Web検索リサーチ
- `/step:outline` - アウトライン生成
- `/step:draft` - 記事執筆
- `/step:check` - 品質チェック

### スタイル管理
- `/style:create` - スタイルガイド作成
- `/style:list` - スタイル一覧表示

### ユーティリティ
- `/util:date` - 日時取得
- `/util:select-project` - プロジェクト選択

## 設定のカスタマイズ

### スタイルガイド

`config/styles/` に独自のスタイルガイドを追加できます。

### プリセット設定

`config/brief/` によく使う設定をプリセットとして保存できます。

### textlint設定

`config/textlint/` とルートの `.textlintrc.json` で校正ルールを調整できます。

## 詳細ドキュメント

- [ワークフロー仕様書](docs/WRITING_WORKFLOW.md)
- [コマンド作成ガイドライン](docs/COMMAND_GUIDELINES.md)

## ライセンス

MIT License

## 貢献

Issue や Pull Request をお待ちしています。