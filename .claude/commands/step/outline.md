---
description: "記事のアウトラインを生成"
allowed-tools: Bash, Read, Write
---

# 前提条件
- プロジェクトディレクトリが存在すること
- brief.yamlが作成済みであること
- articles.yamlが作成済みであること
- research.yamlが作成済みであること（推奨）

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

## 3. アウトライン作成対象記事の選択

### 単発記事の場合（type: "single"）
- articles.yamlの唯一の記事を対象とする

### シリーズ記事の場合（type: "series"）
- **全記事のアウトラインを一括で作成**
- シリーズ全体の流れを考慮した一貫性のある構成
- 既存のアウトラインがあっても上書き

※ 記事IDは`01_slug-name`形式（例：`01_ai-collaboration`、`02_future-skills`）

## 4. プロジェクトのresearch.yamlを読み込み
- @projects/[プロジェクト名]/research.yaml（存在する場合）

## 4.5. スタイルガイドの確認（重要）
brief.yamlのstyle_guideフィールドを確認：
- 指定あり（default以外） → @config/styles/[スタイル名].yaml を読み込み
- style_characteristicsのsentence_endingを確認（"de-aru"または"desu-masu"）
- **重要**: style_notesに文体を明記し、スタイルガイドと一致させる

## 5. アウトラインの生成

### 生成時の考慮事項
1. **記事の目的と対象読者**
   - brief.yamlのpurposeとtarget_audienceを中心に構成
   - トーンに合わせた見出しの表現調整

2. **文字数配分**
   - target_lengthに基づいて各セクションの文字数を配分
   - 導入：全体の15-20%
   - 本論：全体の60-70%
   - 結論：全体の10-15%

3. **リサーチ結果の活用**
   - research.yamlのkey_findingsを各セクションに配置
   - statisticsやquotesを効果的に配置

4. **要件の網羅**
   - requirementsのすべての項目が含まれているか確認
   - interview_notesの重要ポイントを反映

### アウトライン構造の例
```yaml
title: "[記事タイトル]"
total_length: [目標文字数]
structure:
  - section: "導入"
    subsections:
      - title: "[フック/問題提起]"
        content_points:
          - "[ポイント1]"
          - "[ポイント2]"
        estimated_length: [文字数]
        research_refs: ["finding_id", "statistic_id"]
      - title: "[記事の概要/読者への約束]"
        content_points:
          - "[ポイント1]"
        estimated_length: [文字数]
    
  - section: "本論"
    subsections:
      - title: "[メインポイント1]"
        content_points:
          - "[詳細1]"
          - "[詳細2]"
        estimated_length: [文字数]
        research_refs: ["source_id"]
      - title: "[メインポイント2]"
        content_points:
          - "[詳細1]"
          - "[詳細2]"
        estimated_length: [文字数]
        
  - section: "結論"
    subsections:
      - title: "[まとめ]"
        content_points:
          - "[要点の再確認]"
        estimated_length: [文字数]
      - title: "[行動への呼びかけ]"
        content_points:
          - "[具体的なアクション]"
        estimated_length: [文字数]

flow_notes: |
  記事全体の流れに関する注記
  各セクション間のつながりや転換の説明

style_notes: |
  文体やトーンに関する具体的な指示
  スタイルガイドの適用方法
  ※必ずスタイルガイドの文体設定（sentence_ending）を確認し、
    "である調"または"ですます調"を明記すること
```

## 6. アウトラインの保存
生成したアウトラインをoutline.yamlとして保存。

保存前に記事ディレクトリを作成：
```bash
mkdir -p projects/[プロジェクト名]/articles/[記事ID]
```

保存先: `projects/[プロジェクト名]/articles/[記事ID]/outline.yaml`

**重要**: 必ず各記事のディレクトリ内にoutline.yamlを保存すること。
別途outlinesディレクトリを作成してはならない。

※ シリーズ記事の場合は、全記事分のディレクトリを作成してそれぞれにoutline.yamlを保存

# 実行後の状態
- 単発記事：対象記事のarticles/[記事ID]/outline.yamlが作成される
- シリーズ記事：全記事のarticles/[記事ID]/outline.yamlが作成される（既存も上書き）
- articles.yamlの該当記事のstatusが"in_progress"に更新される
- 記事の全体構造が明確になる
- 次のステップ（draft）で執筆する準備が整う

# 使用例
```
# 引数あり（プロジェクト指定）
/step:outline ossan-winning-strategy

# 引数なし（対話的選択）
/step:outline
```

# 関連コマンド
- 前: /step:research
- 次: /step:draft
- 利用: /util:select-project