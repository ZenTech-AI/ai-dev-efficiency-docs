# 自社開発スタイルに最適化した ClaudeCode 導入手引き

このドキュメントでは「個々のプログラマー」の生産・効率性を最大化するために、Claude Code の導入の手引きを紹介する。 \*リポジトリ・リーダーレベルでの推奨設定は別途紹介する予定です。

# 目的・背景

- 目的：Claude Code 等の Agentic なツールを使うことで開発の効率化を実現し、実装部分の工数を削減することが目的。
- ClaudeCode を活用した Agentic Coding の社内標準をここで記載します。
- 各々の開発スタイルを尊重するために「根幹」をここで記載し「枝葉」の部分は紹介・推奨にとどめる。

# Quickstart

## Setup

[公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/setup)を参照しつつ、セットアップを行ってください。

## 各 MCP との連携

### Jira MCP

ZenTech では、タスク管理に[Jira](https://www.atlassian.com/software/jira?referer=jira.com)を使用しています。
Claude Code が Jira のタスク情報を把握するために、MCP の設定を行います。

下記のコマンドを実行

```
claude mcp add -s user --transport sse jira https://mcp.atlassian.com/v1/sse
```

claude を起動して、Jira の認証を通す

```
claude
/mcp
```

ブラウザの認証画面が開くので承認する。

![スクリーンショット 2025-07-05 22 22 54](https://github.com/user-attachments/assets/7f7df6a2-c7b4-4e4f-bc50-7a789b15092f)

### Github MCP

#### MCP の設定

下記のコマンド一発でできるはずが Akino の環境では、エラーでできなかったので PAT の方法を記載。

```
claude mcp add --transport sse github-server https://api.github.com/mcp
```

**Github の PAT を発行：**

下記を参考に PAT を発行してください。
https://dev.classmethod.jp/articles/github-personal-access-tokens/
権限は repo をつけておけば OK です。

**PAT を export して MCP 登録：**

```
export GITHUB_PAT="github_pat_XXX"
claude mcp add -s user --transport http github-server https://api.githubcopilot.com/mcp -H "Authorization: Bearer $GITHUB_PAT"
```

### Figma MCP

TBD

設定が完了したら

# 構成の思想・解説

- 前提：Claude Code を使用する開発メンバー
- 個々での開発スタイルを最大限尊重したいと考えている。
- 多くの場合で、開発に必要な情報にアクセスして、その情報を元に開発する前提は変わらないはず
- 繰り返しになるが、個々人の開発スタイルを阻害する意図は無いし、開発を楽しむものであるという考え方は、すごく大事。
- しかしビジネスを遂行する以上、組織的に開発効率化を行うことも同じくらい大事だと考えている。
- 下記に先ほど示したセットアップを前提とした、ユースケースを記載した。ぜひ、これをベースに試してみて、個々人に合うようにカスタマイズしていってほしい。
