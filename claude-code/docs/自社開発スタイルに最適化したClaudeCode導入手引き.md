# 自社開発スタイルに最適化した Claude Code 導入手引き

このドキュメントでは、個々のプログラマーの生産性と効率性を最大化するための Claude Code 導入手引きを紹介します。
※リポジトリ・リーダーレベルでの推奨設定は別途紹介予定です。

# 目的・背景

- **目的**：Claude Code などの Agentic ツールを活用して開発を効率化し、実装（コーディング）工数を削減します
- **対象**：Claude Code を使用して開発効率を向上させたいメンバー
- Claude Code を活用した Agentic Coding の社内標準を記載します
- 各メンバーの開発スタイルを尊重するため、「根幹」を記載し、「枝葉」の部分は紹介・推奨にとどめます

# Quickstart

## Setup

[公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/setup)を参照しつつ、セットアップを行ってください。

## 各 MCP との連携

### （1）Jira MCP

ZenTech では、タスク管理に[Jira](https://www.atlassian.com/software/jira?referer=jira.com)を使用しています。
Claude Code が Jira のタスク情報を把握するために、MCP の設定を行います。

以下のコマンドを実行します：

```
claude mcp add -s user --transport sse jira https://mcp.atlassian.com/v1/sse
```

claude を起動して、Jira の認証を行います：

```
claude
/mcp
```

ブラウザが開いて認証画面が表示されるので、「承認」をクリックしてください。

![スクリーンショット 2025-07-05 22 22 54](https://github.com/user-attachments/assets/7f7df6a2-c7b4-4e4f-bc50-7a789b15092f)

### （2）Github MCP

#### MCP の設定

以下のコマンド一発で設定できるはずですが、一部の環境ではエラーが発生する場合があります。その場合は PAT（Personal Access Token）を使用した方法をお試しください。

```
claude mcp add --transport sse github-server https://api.github.com/mcp
```

**Github の PAT を発行：**

以下の参考記事をもとに PAT を発行してください：
https://dev.classmethod.jp/articles/github-personal-access-tokens/
権限は「repo」にチェックを入れてください。

**PAT を export して MCP 登録：**

```
export GITHUB_PAT="github_pat_XXX"
claude mcp add -s user --transport http github-server https://api.githubcopilot.com/mcp -H "Authorization: Bearer $GITHUB_PAT"
```

### （３）Figma MCP

TBD

### （4）Context7 MCP

最新かつバージョン別のコードドキュメントを提供する MCP サーバー。

```
claude mcp add -s user --transport sse context7 https://mcp.context7.com/sse
```

常に反映させるために`~/.claude/CLAUDE.md`に何かしらの形で追記するのが良いかもしれません。
例：

```
MUST: Always use context7 in any kind of design, planning, or implementation task.
```

https://github.com/upstash/context7

設定が完了したら、実際に使ってみましょう。後述の Example セクションで実例を紹介していますので、まずは手元で試し、各自の開発スタイルに合わせてカスタマイズしてください。
Example を示す前に、組織的に AI を活用した開発効率化に対する考え方を記載します。

# 構成の解説と組織的な開発の向き合い方

## 開発コンテキストの拡充

**タスクを実行する際には、人間・AI を問わず、以下の 3 つのコンテキストが必要です：**

1. **タスクの内容**
   - タスクの目的・背景
   - 実行すべきこと・すべきでないこと
   - AC（受け入れ条件）
2. **開発プロジェクト固有のコンテキスト**
   - 現状のファイル構成・コード
   - プログラミング言語・フレームワーク
3. **一般的な技術的知識**
   - アーキテクチャのベストプラクティス
   - リーダブルなコーディングスキル

これらが欠けると：

- 仕様と異なる成果物になる
- バグ・事故が発生する
- コミュニケーションコストが増大する

従来は、暗黙知として共有されたナレッジをもとに開発を進めるのが主流でした。コンテキスト整理にリソースをかけなくても、Slack や同期ミーティングでのコミュニケーションでカバーできていました。

しかし、AI 駆動開発が主流になった現在、**「人間のコーディング速度 < AI のコーディング速度」** となっています。コンテキスト共有にリソースを投資することでパフォーマンスが向上する可能性が高く、この傾向は今後も加速すると予測されます。

人間は以下に集中することで、開発速度を最適化します：

- バグが起きにくく、AI・人間双方がコンテキストを理解しやすい設計
- AI に渡すコンテキストの整理
- コードレビュー・テスト

## AI とソフトウェアエンジニアリングの融合

### 何でも AI に任せて OK？

Yes でもあり、No でもあります。AI と共に開発を進める場合、以下の 2 つのパターンがあります：

- AI と伴走
- AI に委託

#### AI との協業の 2 つのモード

**● AI と伴走**

- AI と対話しながら直列開発
- コードを書くスピードは（「AI に委託」と比べて）遅い
- コントロールや状況把握の度合いが高い
- Traditional: 決定論的だが、人力のためスケールしない

**● AI に委託**

- 自走する AI に任せて並列開発
- コード生成スピードは圧倒的に速い
- コントロールや状況把握の度合いが低く、レビューが課題
- Emerging: 非決定論的で結果が確率的だが、高いスケーラビリティを持つ

これらのパターンを適切に使い分ける必要があります。

### どの領域に、どの AI を使うか

![スクリーンショット 2025-07-07 0 20 06](https://github.com/user-attachments/assets/fc94a7c1-0523-4c64-b7cf-c6d11922c159)

タスクの性質に応じて、適切な AI を選択する必要があります。

### 参考

https://speakerdeck.com/twada/agentic-software-engineering-findy-2025-07-edition

## 個々の開発スタイルの尊重とビジネスとしての開発効率化

前提として、個々人の開発スタイルを尊重します。IDE を使うメンバーもいれば、Vim を愛用するメンバーもいるでしょう。

しかし、開発に必要な情報へのアクセスと、その情報をもとに開発を進めるという基本は共通しています：

- Jira のタスク内容確認
- Figma でのデザイン確認
- コードベースの現状確認
- 実装・テスト・動作確認
- ブランチ作成・コミット・PR 作成
- コードレビュー依頼・実施

これらは全メンバーに共通する基本的なワークフローです。個々の開発スタイルに依存しない基本動作であり、自動化しても開発スタイルへの影響は限定的です。

Claude Code や Cursor などの AI ツールによって、開発工程は日々進化しています。組織として、ビジネスとして、この変化に適応していく必要があります。

## 組織的な開発効率化に取り組む目的

Claude Code や Cursor などの AI 開発ツールの発達により、今後以下の流れが予測されます：

1. AI 開発ツールの精度向上により、AI によるコーディング作業の代替割合が増加
2. 顧客が「AI 活用前提の価格」を提示し、発注金額が低下

この流れの中で、組織として競争力を維持し、メンバーのスキルセットを AI 時代に適応させる必要があります。

## AI 開発ツールとの今後の向き合い方

今後も新たな AI 開発ツールが登場し続けるでしょう。
継続的にキャッチアップし、組織的に活用して開発効率化を推進していきます。

現在のやり方に固執せず、柔軟に適応しましょう。
AI や開発形態の変化を完璧に予測することは不可能ですが、その時代に最適な開発体制を構築していきます。

# Example（実例）

実際に Claude Code を活用した開発フローを紹介します。これをベースに、各自の開発スタイルに合わせてカスタマイズしてください。

## （1）Jira にタスクを起票

多くの場合、Jira にタスクの内容が記載されています。
タスクの内容やフォーマットに特に縛りはありませんが、具体的に記載することを推奨します。特に「Figma URL」を記載しておくと、開発段階で Figma MCP と連携できて便利です。

基本的なフォーマット例：

```
背景・目的
・xxx
AC(受け入れ条件)
・xxx
やりそうなこと
・xxx
やらないこと
・xxx
参考
・Figma URL：
```

## （2）Jira から Github Issue に実装の仕様書を転記する

Claude Code Command で Jira の内容を Github Issue に転記します。

### Github Issue を使用する理由

#### 理由 0：Jira MCP の不安定性

- 一部の環境で Jira MCP がエラーを起こす場合があります
- 時間経過で解決しますが、不安定さが課題です

#### 理由 1：Claude Code の Issue 連携機能

- Issue 内容を読み込んで PR を作成する機能を活用できます

[Claude Code GitHub Actions - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/github-actions)

#### 理由 2：役割の明確化

- Jira：タスクをカジュアルに記載する場所
- Github Issue：コードコンテキストを含む開発仕様書

#### 理由 3：コンテキスト管理の安全性

- AI が Jira に直接書き込むのはリスクがあります
- PM が Jira に仕様記載 → プログラマーが AI で仕様拡充 → Jira への拡充は情報消失リスク
- Issue は新たなデータソースとして、柔軟な修正・削除が可能

#### 理由 4：透明性とレビュー可能性

- 複雑なタスクは Issue で開発計画を可視化
- 安全かつ高速な開発を実現

### Claude Code に Github Issue を作成するコマンドを作成する

.claude/commands/create-issue.md に下記を追加

```
## Create Issue

最終的な目標はユーザーから与えられた情報を元に`github mcp`を使って指定のGithubリポジトリにIssueを作成することです。
Issueのタイトルには、タスクのタイトルを明確に記述してください。
Issueの説明には、タスクの詳細を指定の出力形式を遵守して出力してください。

### タスクの進め方
（0）プロジェクトのGithubのrepository・owner名をMCPを使って特定。不明の場合はユーザーに質問して。
（1）ユーザーから与えられた情報を理解して。
（2）README.mdやコードを確認して、現状の実装を確認して。
（3）与えられた情報から、複数の実行可能なタスクに分割可能かを判断してください。
（4）分割した方が良い場合は複数、単一の場合は単一のGithub Issueの提案してください。
（5）Github Issueに出力する「前」にユーザーに内容の確認を依頼して。
（6）承認が得られれば、先ほど取得したowner・repository名で、Github Issueを作成して。

### 重要・注意点

- 作成するowner・repositoryが不明な場合は、ユーザーに聞いて。
- Issueを作成することが目的なため、コードの変更・作成は禁止。

### 出力形式・例：Github Issueの説明

[背景]
XXXのタスクの一部として、YYYを実装する必要がある。
このタスクではZZZを適切に設計してプロジェクトの命名規則に従って実装をします。

[事前準備]
- `xxx`（ファイル名）を参照すること
- `xxx`（ファイル名）の既存の実装を参照すること

[やること]
- xxxに定義を記載する
- 関数・変数名xxxの形式を準拠する。
- レスポンスは`XXX`の形式で作成
- 変更後に`xxx`を実行して、実装を確認

[やらないこと]
- xxxには新しい定義を追加しない

[完了チェックリスト]
- XXXを実行して、エラーが出ない

[参考内容]
- Figma URL（任意）：
- Jira URL（任意）：
```

### 使用方法

claude を開き、以下のコマンドを実行：

```
> /create-issue {ここにJiraのコピペやタスクの概要を貼り付ける}
```

Jira の内容から開発仕様書を生成し、Issue を作成します。Issue の例：

![スクリーンショット 2025-07-06 0 34 47](https://github.com/user-attachments/assets/097aadc6-9c89-4899-a50b-84024994ab09)

内容が意図と違う場合は、Claude Code に修正を依頼するか、自分で Issue を編集してください。

## （3）Claude Code とペアプロ ｜ Claude Code Actions にお任せ

### Claude Code を使ってオーケストレート的にタスクを実行

この設定により、複雑なタスクを段階的に分析・分解し、並列にサブタスクを実行できます。ステップごとに結果をレビューし、柔軟に計画を調整可能です。

.claude/commands/orchestrator.md を以下のように編集：

```
# Orchestrator

Split complex tasks into sequential steps, where each step can contain multiple parallel subtasks.

## Process

1. **Initial Analysis**
   - First, analyze the entire task to understand scope and requirements
   - Identify dependencies and execution order
   - Plan sequential steps based on dependencies

2. **Step Planning**
   - Break down into 2-4 sequential steps
   - Each step can contain multiple parallel subtasks
   - Define what context from previous steps is needed

3. **Step-by-Step Execution**
   - Execute all subtasks within a step in parallel
   - Wait for all subtasks in current step to complete
   - Pass relevant results to next step
   - Request concise summaries (100-200 words) from each subtask

4. **Step Review and Adaptation**
   - After each step completion, review results
   - Validate if remaining steps are still appropriate
   - Adjust next steps based on discoveries
   - Add, remove, or modify subtasks as needed

5. **Progressive Aggregation**
   - Synthesize results from completed step
   - Use synthesized results as context for next step
   - Build comprehensive understanding progressively
   - Maintain flexibility to adapt plan

## Example Usage

When given "analyze test lint and commit":

**Step 1: Initial Analysis** (1 subtask)
- Analyze project structure to understand test/lint setup

**Step 2: Quality Checks** (parallel subtasks)
- Run tests and capture results
- Run linting and type checking
- Check git status and changes

**Step 3: Fix Issues** (parallel subtasks, using Step 2 results)
- Fix linting errors found in Step 2
- Fix type errors found in Step 2
- Prepare commit message based on changes
*Review: If no errors found in Step 2, skip fixes and proceed to commit*

**Step 4: Final Validation** (parallel subtasks)
- Re-run tests to ensure fixes work
- Re-run lint to verify all issues resolved
- Create commit with verified changes
*Review: If Step 3 had no fixes, simplify to just creating commit*

## Key Benefits

- **Sequential Logic**: Steps execute in order, allowing later steps to use earlier results
- **Parallel Efficiency**: Within each step, independent tasks run simultaneously
- **Memory Optimization**: Each subtask gets minimal context, preventing overflow
- **Progressive Understanding**: Build knowledge incrementally across steps
- **Clear Dependencies**: Explicit flow from analysis → execution → validation

## Implementation Notes

- Always start with a single analysis task to understand the full scope
- Group related parallel tasks within the same step
- Pass only essential findings between steps (summaries, not full output)
- Use TodoWrite to track both steps and subtasks for visibility
- After each step, explicitly reconsider the plan:
  - Are the next steps still relevant?
  - Did we discover something that requires new tasks?
  - Can we skip or simplify upcoming steps?
  - Should we add new validation steps?

## Adaptive Planning Example


Initial Plan: Step 1 → Step 2 → Step 3 → Step 4

After Step 2: "No errors found in tests or linting"
Adapted Plan: Step 1 → Step 2 → Skip Step 3 → Simplified Step 4 (just commit)

After Step 2: "Found critical architectural issue"
Adapted Plan: Step 1 → Step 2 → New Step 2.5 (analyze architecture) → Modified Step 3

```

### ペアプロ（伴走）の場合

Claude Code で Github MCP 経由で Issue 情報を読み込みながら実装を進めます。Claude を起動し、Issue の URL を渡して実装を依頼：

```
> /orchestrator https://github.com/ZenTech-AI/ai-dev-tools/issues/4の実装を進めてください
```

### Claude Code Actions を使う場合

作成した Issue のコメントに以下を入力し、Github Actions 上で実行：

```
@claude 実装をしてPRを作成して。
```

参考：
https://docs.anthropic.com/ja/docs/claude-code/github-actions

## （4）ブランチを切って git add & commit して PR を作成

Claude Command 一発でブランチ作成、コード変更の git add、commit、PR 作成まで実行します。

.claude/commands/git-add-commit-create-pr.md に以下を追加：

```
## Git add & commit & create PR

最終的な目標はユーザーから与えられた情報を元に`github mcp`を使って指定のGithubリポジトリにPRを作成することです。
PRのタイトルには、実装概要を明確に記述してください。
PRの説明には、実装の詳細を指定の出力形式を遵守して出力してください。

### タスクの進め方
（0）プロジェクトのGithubのrepository・owner名をMCPを使って特定。不明の場合はユーザーに質問して。
（1）git statusやgit diffを駆使して、実装を確認してください。
（2）適切な粒度でcommitできるように計画を立ててください。
（3）ブランチを切ってgit add & git commitをしてPRを作成してください。

### 重要・注意点

- 作成するowner・repositoryが不明な場合は、ユーザーに聞いて。
- PRを作成することが目的なため、コードの変更・作成は禁止。

### 出力形式・例：Github PRの説明

[該当Issue]
{該当するIssueがある場合は、ここに記載。}

[概要]
XXXのタスクの一部として、YYYを実装。
この実装ではZZZの機能を追加し、拡張性と保守性を高めました

[原因と対処法]（バグ修正の場合）
{何が原因と判断し、どう対処したのか}

[やったこと]
{対応内容を簡潔にリストアップ}

[変更結果]
{操作動画やスクショ、レスポンス内容など}

[やらないこと]
{このPRでスコープ外とする内容}

[注意事項]
{マージした後はこのコマンドを実行してね、などメンバーに知らせるべき内容}

[課題]
{悩んでいるところ、とくにレビューしてほしいところ（これは直接ソースにコメントしてもいいと思います。）}

[備考]
{その他追記事項、関連資料や参考資料などをまとめる}
```

以下のコマンドで現在の実装を確認し、適切な粒度で commit して PR 作成まで実行：

```
> /git-add-commit-create-pr
```

## （5）人間レビュー & Claude Code レビュー

※リポジトリ作成時に設定することを推奨します。

claude.yml を以下のように設定：

```
// claude.yml

// ...(略)

- name: Run Claude Code
  id: claude
  uses: anthropics/claude-code-action@beta
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }
    custom_instructions: |
      - レビュー観点は @docs/REVIEW.md にあります。これを参考にして、絶対に抜け漏れがないように ultrathink して考えてください。
      - 常に日本語で返信してください。
```

次に、docs/README.md にレビュー観点を記載：

```
# コードレビューガイドライン
LLM を活用して **AI・新卒メンバーが生成したコード** をレビューし、以下を実現します：
- **破壊的変更・デグレを未然に防止**
- **バグ・セキュリティ欠陥を早期に検出**
- **保守性・安全性の高いコード品質を維持・向上**

LLM には本ガイドラインを含むプロンプトを渡し、コメントには **「MUST / WANT / FYI / NITS」** のラベル付けを必須とします。

## レビューポイント

> 各観点の下に *MUST / WANT / FYI / NITS* でチェック内容を記載すると、LLM が自動的に優先度別コメントを返しやすくなります。

### 1. 仕様・機能の正当性
- **MUST**: 仕様書・ユーザーストーリーとコードの挙動が一致しているか。
- **WANT**: 追加仕様や将来拡張に干渉しない設計になっているか。
- **FYI**: 同様の機能を持つ既存モジュールや標準ライブラリがないか。

### 2. 互換性・破壊的変更抑止
- **MUST**: 公開 API／データモデルのシグネチャ変更がないか。
- **NITS**: リファクタリング由来の import 並び順の変更など。

### 3. セキュリティ
- **MUST**: インジェクション・XSS・CSRF など既知の脆弱性対策があるか。
- **MUST**: 秘密情報（API キー・パスワード）がハードコードされていないか。
- **WANT**: 権限境界が明確に分離されているか（Zero-Trust の考え方）。

### 4. 可読性・保守性
- **MUST**: 命名が一貫しており意図を表しているか。
- **MUST**: 循環依存・巨大クラスなどアンチパターンを含まないか。
- **WANT**: 関数／クラスの責務が単一でテストしやすい分割になっているか。
- **NITS**: コメントの言語統一、改行・インデント・空行などスタイル細部。

### 5. エラーハンドリング & ロギング
- **MUST**: 例外が握りつぶされず適切に伝搬／処理されるか。
- **FYI**: 既存ログ基盤のフォーマット・レベル指針。

### 6. ドキュメンテーション
- **NITS**: サンプルコードのインデントやコメント表記揺れ。
```
