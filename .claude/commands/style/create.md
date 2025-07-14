---
description: "既存文章からスタイルガイドを生成"
allowed-tools: Read, Write, Glob
---

# 前提条件
- 分析対象の文章ファイルが存在すること
- config/stylesディレクトリが存在すること（なければ作成）

# 実行内容

## 1. スタイルガイドの名前を決定
引数チェック（$ARGUMENTS）：
- 引数あり → それをスタイルガイド名として使用
- 引数なし → ユーザーに名前の入力を求める

## 2. 分析対象ファイルの選択
以下のいずれかの方法で対象ファイルを特定：

### オプション1: 特定のファイルを指定
どのファイルを分析しますか？
1) 特定のファイルパスを入力
2) ディレクトリ内の全ファイルを分析
3) パターンで検索（例: *.md, articles/*.txt）

### オプション2: サンプル文章の入力
ファイルがない場合は、サンプル文章を直接入力してもらう

### ファイル数による処理の違い
- **20ファイル以下**: 全ファイルを詳細に分析
- **21ファイル以上**: サンプリング分析（代表的なファイルを選択して分析）

ファイル数が多い場合は、以下のメッセージを表示：
```
[数]個のファイルが見つかりました。
21ファイル以上のため、サンプリング分析を行います。
```

## 3. 文章の分析

### 分析項目
1. **文体の特徴**
   - 文末表現（です・ます調、である調、混在など）
   - 文の長さの傾向（短文/長文/バランス型）
   - 段落構成の特徴

2. **語彙の特徴**
   - 専門用語の使用頻度
   - カタカナ語の使用傾向
   - 難易度レベル（simple/standard/advanced）

3. **構成の特徴**
   - 見出しの付け方
   - 論理展開のパターン
   - 具体例の使い方

4. **トーンとマナー**
   - フォーマル度
   - 感情表現の有無
   - 読者との距離感

5. **特徴的な表現**
   - よく使われるフレーズ
   - 接続詞の使い方
   - 強調表現のパターン

## 4. スタイルガイドの生成

```yaml
name: "[スタイルガイド名]"
description: "[このスタイルの簡潔な説明]"
created_at: "[作成日時]"
# 参照ファイル（分析結果は下記の特性に反映済み。執筆時はこれらのファイルを読み込まないこと）
source_files:
  - "[分析元ファイル1]"
  - "[分析元ファイル2]"

# 文体特性
style_characteristics:
  sentence_ending: "desu-masu" | "de-aru" | "mixed"
  sentence_length: "short" | "medium" | "long" | "varied"
  paragraph_length: "short" | "medium" | "long"
  formality_level: 1-5  # 1:very casual, 5:very formal

# 語彙特性
vocabulary:
  complexity: "simple" | "standard" | "advanced"
  technical_terms: "none" | "minimal" | "moderate" | "heavy"
  katakana_usage: "minimal" | "moderate" | "frequent"
  preferred_words:
    - instead_of: "困難"
      use: "難しい"
    - instead_of: "実施"
      use: "行う"

# 構成パターン
structure_patterns:
  opening_style: "[導入の特徴]"
  development_style: "[展開の特徴]"
  closing_style: "[締めの特徴]"
  transition_phrases:
    - "また、"
    - "一方で、"
    - "つまり、"

# トーンマーカー
tone_markers:
  emotional_expressions: true | false
  personal_pronouns: true | false
  reader_engagement: "direct" | "indirect" | "neutral"
  humor_level: 0-3  # 0:none, 3:frequent

# 具体的なルール
style_rules:
  - "数字は原則として算用数字を使用"
  - "専門用語は初出時に説明を付ける"
  - "1文は60文字以内を目安とする"
  - "段落は3-5文でまとめる"

# 例文ペア
examples:
  - category: "説明文"
    before: "[変換前の一般的な文]"
    after: "[このスタイルで書き直した文]"
  - category: "意見表明"
    before: "[変換前の文]"
    after: "[スタイル適用後の文]"

# 注意事項
cautions:
  - "避けるべき表現：[例]"
  - "使いすぎ注意：[例]"
```

## 5. スタイルガイドの保存
生成したスタイルガイドを保存。

保存先: `config/styles/[スタイルガイド名].yaml`

!mkdir -p config/styles

# 実行後の状態
- スタイルガイドファイルが作成される
- `/step:draft` で使用可能になる
- 今後の記事作成で一貫した文体を保てる

# 使用例
```
# 引数あり（スタイル名指定）
/style:create my-blog-style

# 引数なし（対話的に作成）
/style:create
```

# 関連コマンド
- 次: /style:list
- 次: /style:update
- 利用元: /step:draft