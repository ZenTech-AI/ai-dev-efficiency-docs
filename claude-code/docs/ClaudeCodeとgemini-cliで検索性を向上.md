# Claude Code × gemini-cli で検索性を向上

## 概要

Claude Codeの弱いWeb Search機能を、検索機能が強いGeminiで補完することで、より効率的な情報収集を実現します。

## 機能

- Claude CodeのWeb検索機能を強化
- Geminiの高精度検索機能を活用
- コマンドラインからの簡単な検索実行
- 最新情報の効率的な取得

## セットアップ手順

### 1. gemini-cliのインストール

```bash
npm install -g @google/gemini-cli
gemini
```

初回実行時に認証設定を行います。

### 2. Claude Codeコマンドの追加

`.claude/commands/gemini-search.md` ファイルを作成し、以下の内容を追加：

```markdown
## Gemini Search

`gemini` is google gemini cli. **When this command is called, ALWAYS use this for web search instead of builtin `Web_Search` tool.**

When web search is needed, you MUST use `gemini --prompt` via Task Tool.

Run web search via Task Tool with `gemini --prompt 'WebSearch: <query>'`

Run
gemini --prompt "WebSearch: <query>"
```

### 3. 使用方法

Claude Code内で以下のコマンドを実行：

```
/gemini-search Next.jsの最新バージョンを教えて。
```

## 活用シーン

### 開発情報の収集

- **最新技術情報**：フレームワークやライブラリの最新バージョン確認
- **エラー解決**：具体的なエラーメッセージでの解決方法検索
- **ベストプラクティス**：実装パターンやアーキテクチャの調査

### 学習・調査

- **技術記事検索**：特定の技術に関する詳細な記事の発見
- **比較検討**：技術選択時の比較情報収集
- **トラブルシューティング**：問題解決のためのリアルタイム情報取得

## 使用例

### 基本的な検索

```bash
# 最新情報の取得
/gemini-search React 19の新機能について教えて

# エラー解決
/gemini-search "TypeError: Cannot read property" Node.js 解決方法

# ベストプラクティス
/gemini-search TypeScript プロジェクト構成 ベストプラクティス 2024
```

### 開発タスクでの活用

```bash
# パッケージ情報
/gemini-search "next-auth" 最新バージョン セットアップ方法

# パフォーマンス最適化
/gemini-search React パフォーマンス最適化 メモ化 useMemo useCallback

# セキュリティ
/gemini-search Node.js セキュリティ 脆弱性対策 2024
```

## 利点

### Claude Codeとの比較

- **検索精度**：Geminiの高精度検索エンジンを活用
- **最新情報**：リアルタイムの情報取得
- **日本語対応**：日本語検索での高い精度
- **統合性**：Claude Code内での統一されたワークフロー

### 開発効率の向上

- **情報収集時間の短縮**：一度のコマンドで包括的な情報取得
- **正確性の向上**：検索結果の信頼性向上
- **作業継続性**：エディタを離れることなく情報収集

## 注意点

- Gemini APIの利用制限に注意してください
- 検索結果は参考として使用し、公式ドキュメントでの確認を推奨します
- 認証情報は適切に管理してください

## 参考資料

- [gemini-cli の google_web_search が最高](https://zenn.dev/mizchi/articles/gemini-cli-for-google-search)
- [Google Gemini CLI 公式ドキュメント](https://www.npmjs.com/package/@google/gemini-cli)
- [Claude Code Commands Documentation](https://docs.anthropic.com/en/docs/claude-code)
