---
description: "アウトラインに基づいて記事を執筆"
allowed-tools: Bash, Read, Write
---

# 前提条件
- プロジェクトディレクトリが存在すること
- brief.yamlが作成済みであること
- articles.yamlが作成済みであること
- 対象記事のoutline.yamlが作成済みであること
- スタイルガイドが存在すること（指定された場合）

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

## 3. 執筆対象記事の選択

### 単発記事の場合（type: "single"）
- articles.yamlの唯一の記事を対象とする

### シリーズ記事の場合（type: "series"）
- **全記事の原稿を一括で執筆**
- シリーズ全体の一貫性を保った執筆
- 記事間の繋がりや参照を意識した構成

## 4. 必要なファイルを読み込み
- @projects/[プロジェクト名]/research.yaml（存在する場合）
- 単発記事の場合：
  - @projects/[プロジェクト名]/articles/[記事ID]/outline.yaml
- シリーズ記事の場合：
  - 全記事の@projects/[プロジェクト名]/articles/[記事ID]/outline.yaml

## 5. スタイルガイドの読み込み
brief.yamlのstyle_guideフィールドを確認：
- 指定あり → @config/styles/[スタイル名].yaml を読み込み
- 指定なし → デフォルトスタイルで執筆

**重要**: スタイルガイドのsource_filesセクションは参照情報です。
これらのファイルは読み込まず、スタイルガイド内の分析結果のみを使用してください。

## 6. 執筆の実行

### 執筆時の考慮事項

1. **アウトラインの遵守**
   - outline.yamlの構造に従って執筆
   - 各セクションの推定文字数を意識
   - research_refsがある場合は該当データを活用

2. **スタイルガイドの適用**
   - 文末表現、語彙、トーンを統一
   - style_rulesに従った文章構成
   - examplesを参考に文体を調整

3. **文字数管理**
   - 各セクションの文字数をカウント
   - total_lengthに対する進捗を管理
   - ±10%の範囲で調整

4. **リサーチ結果の活用**
   - statisticsを効果的に配置
   - quotesを適切に引用
   - key_findingsを論拠として使用

### 執筆プロセス

1. **セクションごとの執筆**
   - 導入 → 本論 → 結論の順で執筆
   - 各subsectionを一つずつ完成させる
   - flow_notesに従って繋がりを意識

2. **推敲のポイント**
   - requirementsがすべて含まれているか確認
   - interview_notesの重要ポイントが反映されているか
   - 読者（target_audience）に適した内容か

3. **最終調整**
   - 全体の流れと一貫性を確認
   - 文字数の最終調整
   - タイトルと内容の整合性確認

## 7. 原稿の保存

執筆した記事をdraft.mdとして保存。

保存先: `projects/[プロジェクト名]/articles/[記事ID]/draft.md`

※ シリーズ記事の場合は、全記事分のdraft.mdを各記事ディレクトリに保存

### 保存形式
```markdown
# [記事タイトル]

[本文]
```

# 実行後の状態
- 単発記事：対象記事のarticles/[記事ID]/draft.mdが作成される
- シリーズ記事：全記事のarticles/[記事ID]/draft.mdが作成される
- articles.yamlの該当記事のstatusは"in_progress"のまま維持される
- 記事が完成し、次のステップ（check）の準備が整う

# 使用例
```
# 引数あり（プロジェクト指定）
/step:draft ossan-winning-strategy

# 引数なし（対話的選択）
/step:draft
```

# 関連コマンド
- 前: /step:outline
- 次: /step:check
- 利用: /util:select-project
- 利用: /style/*