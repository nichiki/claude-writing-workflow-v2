---
description: "記事のためのリサーチを実行"
allowed-tools: Bash, Read, Write, WebSearch
---

# 前提条件
- プロジェクトディレクトリが存在すること
- brief.yamlが作成済みであること

# 実行内容

## 1. プロジェクトの特定
引数チェック（$ARGUMENTS）：
- 引数あり → そのプロジェクトが存在するか確認
  - 存在する → そのプロジェクトを使用
  - 存在しない → エラーメッセージ
- 引数なし → @.claude/commands/util/select-project.md を実行

## 2. 現在日時を取得
@.claude/commands/util/date.md

## 3. brief.yamlとarticles.yamlの読み込み
選択されたプロジェクトの設定を読み込みます。
@projects/[プロジェクト名]/brief.yaml
@projects/[プロジェクト名]/articles.yaml

## 4. リサーチ対象記事の決定

### 単発記事の場合（type: "single"）
- articles.yamlの唯一の記事を対象とする

### シリーズ記事の場合（type: "series"）
- **全記事をまとめてリサーチ対象とする**
- シリーズ全体を通した一貫性のあるリサーチを実行
- 各記事で共通して使える情報を効率的に収集

## 5. リサーチの実行

読み込んだbrief.yamlの内容に基づいて、以下の観点でリサーチを行います：

### リサーチの観点
- **テーマとキーワード**: brief.yamlのthemeとkeywordsを基に関連情報を検索
- **対象読者のニーズ**: target_audienceが求める情報や視点
- **記事の目的達成**: purposeを達成するために必要な情報
- **要件の充足**: requirementsに記載された必須要素の情報収集
- **シリーズの場合**: 各記事の概要（interview_notes）に基づいた包括的なリサーチ

### リサーチ手法
- Web検索で最新の情報や統計データを収集
- 関連する事例やケーススタディを探す
- 対立する意見や異なる視点も収集（バランスの取れた記事のため）

## 6. リサーチ結果の保存

research.yamlとして以下の形式で保存：

```yaml
conducted_at: "[取得した日時]"
search_queries:
  - "[使用した検索クエリ1]"
  - "[使用した検索クエリ2]"
sources:
  - url: "[URL]"
    title: "[記事タイトル]"
    summary: "[要約]"
    key_points:
      - "[重要ポイント1]"
      - "[重要ポイント2]"
    relevance: "high" | "medium" | "low"
key_findings:
  - category: "[カテゴリ名]"
    finding: "[重要な発見]"
    source_urls: ["URL1", "URL2"]
statistics:
  - data: "[統計データ]"
    source: "[出典]"
    year: "[年]"
quotes:
  - quote: "[引用文]"
    author: "[著者]"
    source: "[出典]"
additional_notes: |
  [追加のメモや考察]
```

保存先: `projects/[プロジェクト名]/research.yaml`
（単発記事・シリーズ記事ともにプロジェクト直下に保存）

# 実行後の状態
- プロジェクト直下にresearch.yamlが保存される
- articles.yamlの該当記事のstatusは"planning"のまま維持される
- 次のステップ（outline）に必要な情報が揃う

# 使用例
```
# 引数あり（プロジェクト指定）
/step:research ai-skillset

# 引数なし（対話的選択）
/step:research
```

# 関連コマンド
- 前: /step:interview
- 次: /step:outline
- 利用: /util:select-project
- 利用: /util:date