---
description: "記事の品質チェックと自動修正"
allowed-tools: Bash, Read, Write, MultiEdit
---

# 前提条件
- プロジェクトディレクトリが存在すること
- articles.yamlが作成済みであること
- 対象記事のdraft.mdが作成済みであること
- textlintがインストール済みであること（なければスキップ）

# 実行内容

## 1. プロジェクトの特定
引数チェック（$ARGUMENTS）：
- 引数あり → そのプロジェクトが存在するか確認
  - 存在する → そのプロジェクトを使用
  - 存在しない → エラーメッセージ
- 引数なし → @.claude/commands/util/select-project.md を実行

## 2. 必要ファイルの読み込み
選択されたプロジェクトから以下を読み込み：
- @projects/[プロジェクト名]/brief.yaml
- @projects/[プロジェクト名]/articles.yaml

## 3. チェック対象記事の選択

### 単発記事の場合（type: "single"）
- articles.yamlの唯一の記事を対象とする

### シリーズ記事の場合（type: "series"）
- **全記事の品質チェックを一括で実行**
- シリーズ全体の一貫性も確認
- 記事間の整合性をチェック

## 4. 必要なファイルを読み込み
- 単発記事の場合：
  - @projects/[プロジェクト名]/articles/[記事ID]/draft.md
  - @projects/[プロジェクト名]/articles/[記事ID]/outline.yaml
- シリーズ記事の場合：
  - 全記事の@projects/[プロジェクト名]/articles/[記事ID]/draft.md
  - 全記事の@projects/[プロジェクト名]/articles/[記事ID]/outline.yaml

brief.yamlのstyle_guideフィールドを確認：
- 指定あり → @config/styles/[スタイル名].yaml を読み込み
- 指定なし → スタイルチェックはスキップ

## 5. 品質チェックの実行

### 5.1 スタイルガイド準拠チェック
スタイルガイドが指定されている場合：
- 文末表現の一貫性
- 語彙の適切性
- トーンの統一性
- 構成パターンの遵守

### 5.2 要件充足チェック
brief.yamlのrequirementsに対して：
- 各要件が記事内で言及されているか
- interview_notesの重要ポイントが反映されているか

### 5.3 アウトライン準拠チェック
outline.yamlの構造に対して：
- 各セクションが存在するか
- 文字数が目標値の±20%以内か

文字数カウントは以下のコマンドで実施：
```bash
# 日本語の文字数を正確にカウント
wc -m projects/[プロジェクト名]/articles/[記事ID]/draft.md
```

### 5.4 textlintチェック

Claude Codeが以下の手順でtextlintを実行します：

**重要**: textlintの存在確認に`which textlint`は使用しないこと。npxでインストールされている場合があるため、直接`npx textlint`を実行して、エラーが出た場合のみスキップする。

1. **自動修正の実行**
   - `npx textlint --fix projects/[プロジェクト名]/articles/[記事ID]/draft.md`
   - コマンド実行時にtextlintが見つからないエラーが出た場合のみスキップ

2. **残存エラーの確認**
   - `npx textlint projects/[プロジェクト名]/articles/[記事ID]/draft.md`
   - エラーが残っている場合は内容を分析

**重要**: textlint --fix実行後も残っているエラーは、必ず6.2のAI修正で対応すること。
ただし、スタイルガイドとの競合やコード例内のエラーなど、修正すべきでないものは除く。

## 6. 自動修正の実行

### 6.1 textlint自動修正
- `textlint --fix`で修正可能なものは即座に適用

### 6.2 AI修正の実行
以下の優先順位で修正を判断：

1. **明らかな誤りの修正**
   - 誤字脱字
   - 文法的エラー
   - 不適切な表現

2. **スタイルガイドとの整合**
   - textlintの指摘がスタイルガイドと競合する場合は、スタイルガイドを優先
   - 理由を記録

3. **修正の制限**
   - 同じエラーに対する修正試行は3回まで
   - 3回試しても修正できない場合は、手動修正推奨として記録

4. **ファイル末尾の改行追加**
   - textlintが「No newline at end of file」エラーを検出した場合
   - **重要**: `echo "" >> file.md` のようなBashコマンドは権限エラーが発生する可能性があるため使用しない
   - 代わりにEditツールを使用：ファイル最後の行を読み取り、改行を追加して置換する

## 7. 文字数の記録

実際の文字数をarticles.yamlに記録：

```bash
# 文字数をカウント
wc -m projects/[プロジェクト名]/articles/[記事ID]/draft.md
```

取得した文字数を、articles.yamlの該当記事に`actual_length`フィールドとして追加・更新します。

## 8. 結果の表示

画面には以下の要約を表示：

```
=== 品質チェック結果 ===

【文字数】
目標: [target_length]文字
実際: [actual_length]文字
差分: [差分]文字 ([パーセント]%)

【textlint結果】
検出: [総数]件 → 修正: [修正数]件
残存: [残存数]件

【総合評価】
✓ スタイルガイド準拠チェック完了
✓ 要件充足チェック完了
✓ アウトライン準拠チェック完了

記事の品質チェックが完了しました。
```

# 実行後の状態
- 単発記事：対象記事のdraft.mdが更新される（自動修正があった場合）
- シリーズ記事：全記事のdraft.mdが更新される（自動修正があった場合）
- articles.yamlの該当記事に`actual_length`フィールドが追加される
- articles.yamlの該当記事のstatusが"completed"に更新される
- 記事が公開可能な品質レベルに達する

# エラー処理
- draft.mdが存在しない場合
  → "エラー: draft.mdが見つかりません。先に/step:draftを実行してください"
- textlintがインストールされていない場合
  → textlintチェックをスキップし、その旨を表示
- プロジェクトが見つからない場合
  → "エラー: 指定されたプロジェクト [プロジェクト名] が見つかりません"

# 使用例
```
# 引数あり（プロジェクト指定）
/step:check ossan-winning-strategy

# 引数なし（対話的選択）
/step:check
```

# 関連コマンド
- 前: /step:draft
- 次: /step:publish（今後実装予定）
- 関連: /util:lint