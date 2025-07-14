# 文章執筆ワークフロー仕様書

## 概要
Claude Codeを使用した文章執筆の自動化ワークフローシステム。
ヒアリングから執筆、品質チェックまでを一貫して管理できる。

## ディレクトリ構成

### コマンドディレクトリ
```
.claude/commands/
├── new.md              # /new - 新規記事作成（フルワークフロー）
├── batch.md            # /batch - 保存済み企画の一括実行
├── step/
│   ├── interview.md    # /step:interview - 対話的インタビュー
│   ├── research.md     # /step:research - リサーチ実行
│   ├── outline.md      # /step:outline - アウトライン生成
│   ├── draft.md        # /step:draft - 執筆（スタイルガイド適用）
│   └── check.md        # /step:check - 品質チェック
├── style/
│   ├── create.md       # /style:create - スタイルガイド作成
│   └── list.md         # /style:list - スタイル一覧表示
└── util/
    ├── date.md         # /util:date - 日時取得ラッパー
    └── select-project.md # /util:select-project - プロジェクト選択
```

### プロジェクトディレクトリ（統一構造）

**重要な設計方針**: 単発記事もシリーズ記事も同じディレクトリ構造を使用します。これにより、stepコマンドの汎用化とコードの保守性を高めます。

#### 単発記事の場合
```
projects/YYYY-MM-DD_プロジェクト名/
├── brief.yaml          # ヒアリング内容（type: "single"）
├── articles.yaml       # 記事のメタ情報（単発の場合は1記事のみ）
└── articles/           # 記事は必ずarticles/以下に配置
    └── 01_記事タイトル/
        ├── research.yaml   # リサーチ結果
        ├── outline.yaml    # アウトライン
        ├── draft.md        # 原稿
        └── check-log.yaml  # 品質チェック結果
```

#### シリーズ記事の場合
```
projects/YYYY-MM-DD_プロジェクト名/
├── brief.yaml          # ヒアリング内容（type: "series"）
├── articles.yaml       # 記事のメタ情報（シリーズの場合は複数記事）
└── articles/           # 複数記事を格納
    ├── 01_第1回タイトル/
    │   ├── research.yaml
    │   ├── outline.yaml
    │   ├── draft.md
    │   └── check-log.yaml
    ├── 02_第2回タイトル/
    │   └── ... （同様の構造）
    └── 03_第3回タイトル/
        └── ...

config/
├── brief/               # 記事テンプレート
│   ├── business.yaml
│   ├── default.yaml
│   └── tech.yaml
├── styles/              # スタイルガイド
│   ├── business.yaml    # ビジネス文書スタイル
│   └── nichiki.yaml     # 個人の文体スタイル
└── textlint/            # textlint設定
    └── prh-rules.yml
```

## ファイルフォーマット

### articles.yaml（単発・シリーズ共通）
```yaml
# 単発記事の場合
articles:
  - id: "01_AIとの協働で生産性を高める方法"
    title: "AIとの協働で生産性を高める方法"
    target_length: 5000
    status: "completed" | "drafting" | "planning"

# シリーズ記事の場合
series_title: "AI時代のキャリア戦略"  # シリーズの場合のみ
flow_notes: |                        # シリーズの場合のみ
  第1回で問題提起、第2回で具体策、第3回で未来展望
  記事間の繋がりや全体の流れに関するメモ
articles:
  - id: "01_AIに負けない強みの見つけ方"
    title: "第1回：AIに負けない強みの見つけ方"
    target_length: 5000
    status: "completed"
    dependencies: []  # 他の記事への依存関係（シリーズの場合のみ）
  - id: "02_AIと協働する具体的手法"
    title: "第2回：AIと協働する具体的手法"
    target_length: 5000
    status: "drafting"
    dependencies: ["01_AIに負けない強みの見つけ方"]
  - id: "03_10年後のキャリアを考える"
    title: "第3回：10年後のキャリアを考える"
    target_length: 5000
    status: "planning"
    dependencies: ["01_AIに負けない強みの見つけ方", "02_AIと協働する具体的手法"]
```

### brief.yaml（単発・シリーズ共通）
```yaml
# プロジェクトレベルのヒアリング内容を記録
# 単発記事でもシリーズ記事でも同じ形式を使用
title: "プロジェクトタイトル"
type: "single" | "series"
# 単発記事の場合
target_length: 5000  # 目標文字数
# シリーズ記事の場合
total_length: 15000  # シリーズ全体の目標文字数
articles_count: 3    # 記事数
target_audience: "対象読者"
purpose: "執筆目的"
tone: "formal" | "casual" | "technical"
style_guide: "nichiki"  # 使用するスタイルガイド
keywords:
  - "キーワード1"
  - "キーワード2"
requirements:
  - "要件1"
  - "要件2"
references: []  # 参考資料
interview_notes: |
  インタビューで得られた詳細情報
  シリーズの場合は各記事の概要も含む
created_at: "2025-07-01 19:39:54 (Tue) JST"
status: "planning" | "researching" | "writing" | "reviewing" | "completed"
```

### research.yaml
```yaml
conducted_at: "2024-01-15T11:00:00+09:00"
sources:
  - url: "https://example.com"
    summary: "要約"
    relevance: "high" | "medium" | "low"
key_findings:
  - "重要な発見1"
  - "重要な発見2"
additional_notes: |
  追加メモ
```

### outline.yaml
```yaml
structure:
  - section: "導入"
    points:
      - "ポイント1"
      - "ポイント2"
    estimated_length: 500
  - section: "本論"
    subsections:
      - title: "サブセクション1"
        points:
          - "ポイント1"
        estimated_length: 1000
```

### スタイルガイド (config/styles/*.yaml)
```yaml
name: "スタイル名"
description: "スタイルの説明"
created_at: "2024-01-15T10:00:00+09:00"
source_articles:  # 分析元の記事
  - "記事パス1"
  - "記事パス2"
characteristics:
  sentence_length: "short" | "medium" | "long"
  paragraph_structure: "説明"
  vocabulary_level: "simple" | "standard" | "advanced"
  tone_markers:
    - "特徴的な表現1"
    - "特徴的な表現2"
style_rules:
  - "ルール1"
  - "ルール2"
examples:
  - original: "元の文"
    styled: "スタイル適用後"
```

## コマンド詳細仕様

### メインワークフロー

#### /new
新規記事をゼロから作成する完全なワークフロー。
1. インタビューセッション
2. 自動リサーチ
3. アウトライン生成
4. 執筆
5. 品質チェック

#### /batch
CSVファイルから複数記事を一括で作成。インタビューをスキップして効率的に記事を量産。
夜間バッチ処理や大量記事作成に最適。

##### CSVフォーマット仕様

| 項目名 | 説明 | 必須/任意 | 例 |
|--------|------|-----------|-----|
| series | シリーズ名。空欄なら単発記事 | 任意 | `お茶の秘密`、`空欄` |
| title | 記事のタイトル | 必須 | `猫の可愛さについて`、`お茶の歴史` |
| target_length | 目標文字数 | 任意 | `3000`、`5000` |
| target_audience | 対象読者 | 任意 | `20〜40代の社会人`、`ビジネスパーソン全般` |
| purpose | 記事の目的 | 必須 | `読者に洞察を与える`、`新しい知識を提供する` |
| tone | 文体のトーン | 任意 | `casual`、`formal`、`technical` |
| style_guide | 使用するスタイルガイド | 任意 | `nichiki`、`business` |
| keywords | キーワード（・区切り） | 任意 | `猫・可愛さ・癒し`、`AI・労働市場・仕事の未来` |
| requirements | 特別な要件（・区切り） | 任意 | `可愛さの具体的な記述`、`科学的根拠も含める` |
| references | 参考URL（・区切り） | 任意 | `https://example.com`、`空欄` |
| notes | インタビューメモ・備考 | 任意 | `猫カフェのすすめも`、`第1回` |

- **動作仕様**:
  - series列が空欄の行は単発記事として処理
  - series列に同じ値が入っている行はシリーズ記事としてグループ化
  - 任意項目が空欄の場合はconfig/briefのデフォルト値を使用
  - テンプレートファイル: `batch_inputs/example.csv`

##### 使い方
1. `batch_inputs/example.csv`をコピーして編集
2. series列が空欄 = 単発記事、値あり = シリーズ記事の一部として処理
3. 必須項目：title, purpose（その他は任意）
4. 区切り文字：キーワードや要件は「・」で区切る
5. `/batch ファイル名.csv`で実行


### 個別ステップコマンド

#### /step:interview
対話的にユーザーから要件をヒアリング。
結果はbrief.yamlとして保存。

#### /step:research
brief.yamlを基にWebリサーチを実行。
必ず実行時の日時を記録。

#### /step:outline
リサーチ結果と要件から記事構成を生成。

#### /step:draft
アウトラインとスタイルガイドに基づいて執筆。

#### /step:check
- スタイルガイド準拠チェック
- 要件充足チェック
- textlintによる文法チェック

### スタイル管理コマンド

#### /style:create
既存記事を分析してスタイルガイドを生成。

#### /style:list
利用可能なスタイルガイド一覧を表示。

### ユーティリティコマンド

#### /util:date
現在日時を取得して適切なフォーマットで返す。

#### /util:select-project
プロジェクトの対話的選択機能。

## 実装における注意点

1. **統一ディレクトリ構造の厳守**
   - 単発記事もシリーズ記事も必ず`articles/`以下に記事を配置
   - stepコマンドは常に`articles/`以下を参照するよう実装
   - articles.yamlで記事のメタ情報を統一管理
   - brief.yamlのtypeフィールドで単発・シリーズを判定

2. **日時の扱い**
   - リサーチ時は必ず現在日時を記録
   - ファイル名にも日付を含める

3. **モジュール化**
   - 各ステップは独立して実行可能
   - 複雑なワークフローは単純なコマンドの組み合わせ

4. **エラーハンドリング**
   - 各ステップで失敗した場合の再開ポイントを明確に
   - 部分的な成功も保存

5. **拡張性**
   - MCPやRAGの追加を想定した設計
   - カスタムチェッカーの追加が容易

6. **stepコマンドの汎用性**
   - 単発・シリーズの判定ロジックは各stepコマンド内で実装
   - brief.yamlのtypeフィールドで判定
   - パスの構築を動的に行う

