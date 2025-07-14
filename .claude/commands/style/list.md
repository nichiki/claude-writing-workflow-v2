---
description: "利用可能なスタイルガイド一覧を表示"
allowed-tools: LS, Read
---

# 前提条件
- config/stylesディレクトリが存在すること

# 実行内容

## 1. スタイルガイドの一覧取得

Claude Codeが以下の手順でスタイルガイドを取得します：

1. `config/styles/`ディレクトリの.yamlファイルを検索
2. ファイル名から拡張子を除いてスタイル名を抽出
3. 各ファイルを読み込んで概要情報を取得

## 2. 各スタイルガイドの概要表示

```
利用可能なスタイルガイド：

1) [スタイル名]
   説明: [description]
   作成日: [created_at]
   フォーマル度: [formality_level]

2) [スタイル名]
   説明: [description]
   作成日: [created_at]
   フォーマル度: [formality_level]
```

## 3. 詳細表示オプション
特定のスタイルガイドの詳細を見たい場合：
- スタイル名を入力してください（詳細表示）
- またはEnterキーで終了

選択された場合は、該当するスタイルガイドの全内容を表示。

# 実行後の状態
- 利用可能なスタイルガイドを把握できる
- 各スタイルの特徴を理解できる

# 使用例
```
/style:list
```

# 関連コマンド
- 前: /style:create
- 次: /style:update
- 利用元: /step:interview
- 利用元: /step:draft