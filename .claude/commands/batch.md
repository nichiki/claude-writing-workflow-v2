---
description: "CSVファイルから複数記事を一括作成"
allowed-tools: Bash, Write, Read, WebSearch, WebFetch
---

# 前提条件
- projectsディレクトリが存在すること
- CSVファイルが正しいフォーマットで用意されていること
- 各stepコマンドが正常に動作すること

# 実行内容

CSVファイルを読み込んで、複数の記事を一括で作成します。

## ステップ1: CSVファイルの指定

まず、使用するCSVファイルを指定してください。

例：
- batch_inputs/my-articles.csv
- batch_inputs/example.csv

**注意**: CSVファイルが存在しない場合は、以下のメッセージを表示して終了：
```
❌ 指定されたCSVファイルが見つかりません: [ファイルパス]

batch_inputs/example.csvを参考に、CSVファイルを作成してください。
```

## ステップ2: CSVファイルの読み込みと検証

CSVファイルを読み込み、内容を確認します。

必須項目の確認：
- title（記事タイトル）
- purpose（記事の目的）

不足がある場合はエラーメッセージを表示して終了。

## ステップ3: 処理対象の確認

読み込んだ内容を整理して表示：

```
📋 処理対象の記事一覧

【単発記事】
1. AIの労働市場への影響 (5000文字)
2. リモートワークの未来 (4000文字)

【シリーズ記事】
猫の可愛さ大全（全4記事）
- 第1回：猫の外見の魅力 (3000文字)
- 第2回：猫の仕草と表情 (3000文字)
- 第3回：猫の行動パターン (3000文字)
- 第4回：猫の性格の魅力 (3000文字)

合計: 6記事を作成します。
続行しますか？ (yes/no)
```

## ステップ4: 記事の一括作成

ユーザーが続行を選択した場合、各記事を順番に処理します。

### 4-1: プロジェクトの作成

各記事/シリーズごとに：
1. 現在日時を取得（@.claude/commands/util/date.md）
2. プロジェクト名を生成（YYYYMMDD_スラッグ形式）
3. プロジェクトディレクトリを作成
4. brief.yamlを作成（CSVデータから変換）
5. articles.yamlを作成

### 4-2: 各ステップの実行

作成したプロジェクトごとに以下を実行：

1. **リサーチ実行**
   @.claude/commands/step/research.md [プロジェクト名]

2. **アウトライン生成**
   @.claude/commands/step/outline.md [プロジェクト名]

3. **記事執筆**
   @.claude/commands/step/draft.md [プロジェクト名]

4. **品質チェック**
   @.claude/commands/step/check.md [プロジェクト名]

各ステップの実行前に進捗を表示：
```
[1/6] AIの労働市場への影響
  ✓ プロジェクト作成完了
  → リサーチ実行中...
```

## ステップ5: 実行結果のまとめ

全ての処理が完了したら、結果をまとめて表示：

```
✨ バッチ処理が完了しました！

【成功】5記事
- AIの労働市場への影響 → projects/20250714_ai-labor-market/
- リモートワークの未来 → projects/20250714_remote-work/
- 猫の可愛さ大全（全4記事） → projects/20250714_cat-cuteness/

【エラー】1記事
- 初心者のためのPython：リサーチステップでエラー

処理時間: 約15分
```

# エラー処理

各記事の処理中にエラーが発生した場合：
1. エラー内容を記録
2. 次の記事の処理を継続
3. 最後に失敗した記事の一覧を表示

# CSVフォーマットからbrief.yamlへの変換規則

- theme: titleを使用（シリーズの場合はseriesを使用）
- type: series列が空なら"single"、値があれば"series"
- target_length: CSVのtarget_lengthを使用（一記事あたりの文字数）
- articles_count: シリーズの場合、同じseries名の行数をカウント（単発の場合は1）
- total_length: target_length × articles_count で計算
- keywords: 「・」区切りをリスト形式に変換
- requirements: 「・」区切りをリスト形式に変換
- references: 「・」区切りをリスト形式に変換
- interview_notes: notes列の内容を使用
- created_at: 実行時の日時
- status: "planning"

# 使用例

```
# CSVファイルを指定してバッチ実行
/batch batch_inputs/my-articles.csv

# サンプルファイルで実行
/batch batch_inputs/example.csv
```

# 関連コマンド
- 利用: /util:date
- 利用: /step:research
- 利用: /step:outline
- 利用: /step:draft
- 利用: /step:check
- 関連: /new（単一記事の対話的作成）