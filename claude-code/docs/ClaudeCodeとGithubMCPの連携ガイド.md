# Claude Code × Github MCP 連携ガイド

## 概要

Claude CodeとGithub MCPを連携することで、GitHubのリポジトリ操作やIssue管理をClaude Code内で直接実行できます。

## 機能

- GitHubリポジトリの直接操作
- Issue・Pull Requestの作成・管理
- リポジトリ情報の取得・検索
- コミットやブランチの管理

## セットアップ手順

### 1. MCP接続の試行（エラーが発生する場合あり）

```bash
claude mcp add --transport sse github-server https://api.github.com/mcp
```

### 2. Personal Access Token（PAT）による接続

上記のコマンドでエラーが発生した場合は、PATを使用した接続を行います。

#### GitHub PATの発行

1. GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. "Generate new token (classic)" をクリック
3. 必要な権限を選択：
   - `repo`: リポジトリへのフルアクセス（推奨）
   - その他必要に応じて追加
4. トークンを生成・保存

#### MCP接続の設定

```bash
export GITHUB_PAT="github_pat_XXX"
claude mcp add -s user --transport http github-server https://api.githubcopilot.com/mcp -H "Authorization: Bearer $GITHUB_PAT"
```

## Claude CodeのMCPスコープ

### ローカルスコープ（`local`）【デフォルト】

- プロジェクト内の個人用設定
- 機密情報や実験的な設定に適している

```bash
claude mcp add my-server /path/to/server
claude mcp add -s local my-server /path/to/server
```

### プロジェクトスコープ（`project`）

- チーム全体で共有する設定
- `.mcp.json` ファイルに保存され、バージョン管理対象

```bash
claude mcp add -s project shared-server /path/to/server
```

### ユーザースコープ（`user`）

- 全プロジェクトで使用する個人用設定

```bash
claude mcp add -s user my-global-server /path/to/server
```

## 使用方法

### Issueの作成

```
XXというリポジトリにIssue作成して。内容はYY
```

### Pull Requestの作成

```
XXリポジトリにPull Request作成して。タイトルはYY、内容はZZ
```

### リポジトリ情報の取得

```
XXリポジトリの最新のコミット情報を教えて
```

## 参考資料

- [Claude Code MCP公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [Claude Code リモートMCP活用ガイド](https://dev.classmethod.jp/articles/shuntaka-support-claude-code-remote-mcp/)
- [GitHub Personal Access Tokens設定ガイド](https://dev.classmethod.jp/articles/github-personal-access-tokens/)