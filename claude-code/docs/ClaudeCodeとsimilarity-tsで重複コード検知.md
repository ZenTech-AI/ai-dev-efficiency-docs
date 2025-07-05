# Claude Code × similarity-ts：重複コード検知ガイド

## 概要

similarity-tsを使用してTypeScript/Pythonの重複コードを検知し、Claude Codeでリファクタリングを効率的に行う方法を説明します。

## 機能

- TypeScript/Pythonの重複コードの自動検知
- 意味的に類似したコードパターンの発見
- Claude Codeとの連携によるリファクタリング支援
- 複数言語対応（TypeScript、Python）

## セットアップ手順

### 1. similarity-tsのインストール

Rustのcargo経由でインストールします：

```bash
cargo install similarity-ts
cargo install similarity-py
```

### 2. 使用方法

プロジェクトのルートディレクトリで実行します：

```bash
# TypeScriptプロジェクトの場合
similarity-ts

# Pythonプロジェクトの場合
similarity-py
```

### 3. 詳細オプション

利用可能なオプションを確認：

```bash
similarity-ts -h
```

## Claude Codeとの連携

### 重複コード検知とリファクタリング

Claude Codeでの活用例：

```
Run `similarity-ts .` to detect semantic code similarities. Execute this command, analyze the duplicate code patterns, and create a refactoring plan. Check `similarity-ts -h` for detailed options.
```

### 実行手順

1. **重複コード検知**
   ```bash
   similarity-ts .
   ```

2. **結果の分析**
   - 検知された重複コードパターンを確認
   - 類似度スコアを評価
   - リファクタリング対象の優先度を決定

3. **リファクタリング計画の作成**
   - 共通化可能なロジックを特定
   - 関数・クラスの抽出計画を立案
   - 影響範囲の分析

4. **Claude Codeによる実装**
   - 検知結果を基にリファクタリングを実行
   - テストケースの作成・実行
   - コードレビュー

## 活用シーン

### 開発効率の向上

- **コードレビュー時**：PRでの重複コード検知
- **リファクタリング時**：技術的負債の解消
- **新機能開発時**：既存コードとの類似性確認

### 品質向上

- **保守性の向上**：重複コードの削減
- **バグ修正効率化**：類似バグの一括修正
- **コード統一性**：チーム間でのコーディング規約遵守

## 注意点

- 大規模プロジェクトでは実行時間が長くなる場合があります
- 検知結果は参考として使用し、最終的な判断は人間が行ってください
- リファクタリング前には必ずテストを実行してください

## 参考資料

- [similarity-ts でAIと人間が書き散らした重複コードを見つける](https://zenn.dev/mizchi/articles/introduce-ts-similarity)
- [similarity-ts GitHub Repository](https://github.com/mizchi/similarity-ts)
- [Claude Code公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code)