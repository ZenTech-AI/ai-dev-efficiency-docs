# 自社開発スタイルに最適化した Claude Code 導入手引き

このドキュメントでは、個々のプログラマーの生産性・効率性を最大化するために、Claude Code の導入手引きを紹介します。
※リポジトリ・リーダーレベルでの推奨設定は別途紹介する予定です。

# 目的・背景

- 目的：Claude Code 等の Agentic なツールを使うことで開発の効率化を実現し、実装部分の工数を削減することが目的です。
- 対象：Claude Code を使用して開発効率を向上させたいメンバー
- Claude Code を活用した Agentic Coding の社内標準をここで記載します。
- 各々の開発スタイルを尊重するために「根幹」をここで記載し、「枝葉」の部分は紹介・推奨にとどめます。

# Quickstart

## Setup

[公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/setup)を参照しつつ、セットアップを行ってください。

## 各 MCP との連携

### Jira MCP

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

### Github MCP

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

### Figma MCP

TBD

設定が完了したら、実際に使ってみましょう。後述の Example セクションで実例を紹介していますので、まずは手元で試し、それぞれの開発スタイルに合わせてカスタマイズしてください。

# 構成の解説と組織的な開発の向き合い方

## 開発コンテキストの拡充

安全かつ高速な開発を実現することが目的です。

**タスクを実行する際には、人間が開発する場合でも、AI が開発する場合でも、以下の 3 つのコンテキストが存在します：**

1. **タスクの内容**
   1. タスクの目的・背景
   2. 何をすれば良いか、何をするべきでないか
   3. AC（受け入れ要件）
2. **開発プロジェクト固有のコンテキスト**
   1. 現状のファイル構成・コード
   2. プログラミング言語・フレームワーク
3. **一般的な技術的知識**
   1. アーキテクチャのベストプラクティス
   2. リーダブルコード的なコーディングスキル

これらが欠けていると：

- 仕様と異なるものが上がってくる
- バグ・事故につながる
- コミュニケーションコストが高まる

以前までは、暗黙の了解としてメンバー間で共有されたナレッジをもとに開発を進めるのが主流でした。コンテキスト整理にリソースをかけなくても、Slack や同期 MTG 等でのコミュニケーションでカバーできていました。

しかし、AI 駆動開発が主流になった昨今、**「人間のコーディング速度 < AI のコーディング速度」**となりました。現状でもコンテキスト共有にリソースをかけた方がパフォーマンスが出る可能性があり、この流れは加速していくと予測しています。

- AI に渡すコンテキスト整理
- コードレビュー・テスト

に人間は集中することで、開発速度の最適化を図ります。

## 個々の開発スタイルの尊重と、ビジネスとしての開発効率化

前提として、個々人の開発スタイルを尊重したいと考えています。例えば、IDE を使うメンバーもいれば、Vim を愛用しているメンバーもいるでしょう。

多くの場合、開発に必要な情報にアクセスし、その情報をもとに開発を進めるという前提は共通しています。具体的には：

- Jira に起票されているタスクの内容を確認
- Figma でデザインを確認
- コードベースから現在の実装を確認
- 実装を行い、テスト・動作確認
- 実装が完了したらブランチを切って、commit して PR を作成
- コードレビュー依頼・実施

これらはどの開発メンバーも共通して行う基本的なワークフローです。これらの動作は個々の開発スタイルに依存するものではなく、基本的な動作だと考えます。これらの基本的な動作を自動化したとしても、多くの場合において開発スタイルに影響しないと考えます。

また、Claude Code や Cursor 等の AI ツールによって、プログラマーの開発工程は日々変化しています。組織的かつビジネスとして開発を行うために、この流れに適応していく必要があると考えています。

## 組織的な開発効率化に取り組む目的

Claude Code や Cursor 等の AI 開発ツールの発達により、今後以下のような流れが来ると予測しています：

（1）AI 開発ツールの精度が向上し、コーディング作業を AI が代替する割合が増える
（2）顧客は「AI を使う前提での価格」を提示し始めて、発注金額が下がる

このような流れの中で、組織として競争力を維持し、メンバーのスキルセットを AI 時代に適応させていく必要があります。

## AI 開発ツールとの今後の向き合い方

今後も AI 開発ツールが次々と出てくると考えています。
その際にキャッチアップを欠かさず、組織的に活用して開発効率化を推進していきたいと考えています。

現在のやり方に固執せず、柔軟に変えていきましょう。

# Example

ここでは実際に Claude Code を活用した開発フローを紹介していきます。ぜひこれをベースに、ご自身の開発スタイルに合わせてカスタマイズしてみてください。

## （1）Jira にタスクを起票

多くの場合、Jira にタスクの内容が記載されています。
基本的に Jira に記載するタスクの内容やフォーマットに縛りはありませんが、具体的に記載しておくと良いでしょう。特に「Figma URL」を記載しておくと、開発段階で Figma MCP と連携できて便利です。

以下に、基本的なフォーマットを示します：

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

Claude Code Command で Jira に記載されている内容を Github Issue に転記していきます。

### Github Issue を使用する理由

#### 理由（0）：Jira の MCP が不安定

- 一部の環境で Jira の MCP を使用するとエラーが発生する場合があります。
- 筆者の環境でも数回遭遇し、時間が経てば解決するものの、不安定さを感じています。

#### **理由（1）：Claude Code には Issue の内容を読み込んで、PR を作成する機能があるため**

- この機能を積極的に活用していきましょう。

[Claude Code GitHub Actions - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/github-actions)

#### **理由（2）：Jira は即座にタスクの起票 | Github Issue は開発の仕様書的な棲み分けをするため**

- Jira をカジュアルにタスクを書き込む場所として活用します。
- Github Issue は、コードのコンテキストを含めて開発の仕様書を記載する場所として活用します。

#### 理由（3）：コード生成を行うためのコンテキストとしての役割を果たすため

- Jira に対して AI が直接書き込みを行うのはリスクがあると感じました。
- PM が Jira に仕様を書く → プログラマーが AI を使って仕様を拡充 → この拡充先が Jira の場合、前の情報が消えたり、意図しない出力が発生した際に問題となる可能性があります。
  - Issue であれば、新たなデータソースなので、必要に応じて柔軟に修正や削除が可能です。

#### 理由（4）他メンバーに可視化して、レビューを可能にするため

- 複雑なタスクを着手する前に Issue で開発計画段階を挟むことにより、安全かつ高速な開発を実現します。

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

claude を開き、下記のようにコマンドを実行。

```
> /create-issue {ここにJiraのコピペやタスクの概要を貼り付ける。}
```

こうすることで、Jira の内容から開発の仕様書を生成して、Issue を作成してくれます。Issue の例は下記です。

![スクリーンショット 2025-07-06 0 34 47](https://github.com/user-attachments/assets/097aadc6-9c89-4899-a50b-84024994ab09)

内容が意図と違う場合は Claude Code に修正してもらうか、自分で Issue を編集する。

## （3）Claude Code とペアプロ｜ Claude Code Actions にお任せ

### Claude Code を使ってオーケストレイト的にタスクを実行させる。

この設定を行うことで、複雑なタスクを段階的に分析・分解し、並列にサブタスクを実行可能。ステップごとに結果をレビューして、柔軟に計画を調整することができます。

.claude/commands/orchestrator.md を下記のように編集してください。

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

### ペアプロの場合

Claude Code で Github MCP 経由で、Issue 情報を読み込みつつ、実装を進めます。Claude を起動して、Issue の URL を渡して実装してもらいます。

```
> /orchestrator https://github.com/ZenTech-AI/ai-dev-tools/issues/4の実装を進めてください
```

### Claude Code Actions を使う場合

作成した Issue のコメントに対して下記を入力し、Github Actions 上で実行。

```
@claude 実装をしてPRを作成して。
```

## （4）ブランチを切って git add & commit して PR を作成

Claude Command 一発でブランチを切って、コードの変更を git add して、commit、PR 作成まで行う。

.claude/commands/git-add-commit-create-pr.md に下記を追加

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

以下のコマンドで現在の実装を確認し、適切な粒度で commit して PR 作成まで代行します：

```
>  /git-add-commit-create-pr
```

## （5）人間レビュー&Claude Code レビュー

※これはリポジトリを作成した時に設定した方が良いかもしれません。

claude.yml を以下のように設定します：

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

次に、docs/README.md に対してレビュー観点を記載します：

```
# コードレビューガイドライン
LLM を活用して **AI・新卒メンバーが生成したコード** をレビューし、
- **破壊的変更・デグレを未然に防止**
- **バグ・セキュリティ欠陥を早期に検出**
- **保守性・安全性の高いコード品質を維持／向上**
させることを目的とする。
LLM には本ガイドラインを含むプロンプトを渡し、コメントには **「MUST / WANT / FYI / NITS」** のラベル付けを必須とする。

## レビューポイント

> ▶ 各観点の下に *MUST / WANT / FYI / NITS* でチェック内容を記載しておくと、LLM が自動的に優先度別コメントを返しやすくなる。

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
