# Claude Code Implementation Guide Optimized for In-House Development Style

This document provides a guide for implementing Claude Code to maximize individual programmer productivity and efficiency.
※Repository/leader-level recommended settings will be introduced separately.

# Purpose & Background

- Purpose: To achieve development efficiency by using Agentic tools like Claude Code and reduce implementation workload.
- Target: Members who want to improve development efficiency using Claude Code
- This document establishes the in-house standards for Agentic Coding using Claude Code.
- To respect individual development styles, we document the "core" principles here while keeping "peripheral" aspects as recommendations.

# Quickstart

## Setup

Please set up by referring to the [official documentation](https://docs.anthropic.com/ja/docs/claude-code/setup).

## Integration with Various MCPs

### Jira MCP

At ZenTech, we use [Jira](https://www.atlassian.com/software/jira?referer=jira.com) for task management.
To enable Claude Code to understand Jira task information, we configure the MCP.

Execute the following command:

```
claude mcp add -s user --transport sse jira https://mcp.atlassian.com/v1/sse
```

Launch claude and authenticate with Jira:

```
claude
/mcp
```

A browser will open showing the authentication screen. Click "Approve".

![Screenshot 2025-07-05 22 22 54](https://github.com/user-attachments/assets/7f7df6a2-c7b4-4e4f-bc50-7a789b15092f)

### Github MCP

#### MCP Configuration

The following command should set it up in one go, but errors may occur in some environments. In that case, please try the method using PAT (Personal Access Token).

```
claude mcp add --transport sse github-server https://api.github.com/mcp
```

**Issue a Github PAT:**

Please issue a PAT based on the following reference article:
https://dev.classmethod.jp/articles/github-personal-access-tokens/
Check the "repo" permission.

**Export PAT and register MCP:**

```
export GITHUB_PAT="github_pat_XXX"
claude mcp add -s user --transport http github-server https://api.githubcopilot.com/mcp -H "Authorization: Bearer $GITHUB_PAT"
```

### Figma MCP

TBD

Once the setup is complete, let's try using it. We introduce practical examples in the Example section below, so please try them locally first and customize them according to your individual development style.

# Configuration Explanation and Organizational Development Approach

## Development Context Enhancement

The goal is to achieve safe and fast development.

**When executing tasks, whether developed by humans or AI, three types of context exist:**

1. **Task Content**
   1. Task purpose & background
   2. What should be done and what should not be done
   3. AC (Acceptance Criteria)
2. **Development Project-Specific Context**
   1. Current file structure & code
   2. Programming language & framework
3. **General Technical Knowledge**
   1. Architecture best practices
   2. Readable code coding skills

When these are lacking:

- Specifications don't match the output
- Leads to bugs and incidents
- Communication costs increase

Previously, it was common to proceed with development based on knowledge implicitly shared among team members. It was possible to cover context organization without dedicating resources to it through communication via Slack or synchronous meetings.

However, with AI-driven development becoming mainstream, **"human coding speed < AI coding speed"** has become the reality. Even now, dedicating resources to context sharing may yield better performance, and we predict this trend will accelerate.

Humans will focus on:

- Context organization to pass to AI
- Code review & testing

to optimize development speed.

## Respecting Individual Development Styles and Business Development Efficiency

As a premise, we want to respect individual development styles. For example, some members use IDEs while others prefer Vim.

In most cases, the premise of accessing information necessary for development and proceeding with development based on that information is common. Specifically:

- Check task content issued in Jira
- Check designs in Figma
- Check current implementation from codebase
- Implement, test, and verify operation
- Create branch, commit, and create PR when implementation is complete
- Request and conduct code review

These are basic workflows that all development members perform in common. These operations don't depend on individual development styles and are considered basic operations. Even if these basic operations are automated, they won't affect development styles in most cases.

Additionally, AI tools like Claude Code and Cursor are changing programmers' development processes daily. We believe it's necessary to adapt to this trend for organizational and business development.

## Purpose of Organizational Development Efficiency

With the development of AI development tools like Claude Code and Cursor, we predict the following trends:

(1) AI development tools will improve in accuracy, increasing the proportion of coding work replaced by AI
(2) Customers will start proposing "prices assuming AI use" and order amounts will decrease

In this trend, we need to maintain organizational competitiveness and adapt member skill sets to the AI era.

## Future Approach to AI Development Tools

We believe AI development tools will continue to emerge.
We want to keep up with these developments and promote organizational development efficiency.

Let's be flexible and change without clinging to current methods.

# Example

Here we introduce actual development flows using Claude Code. Please use this as a base and customize it according to your development style.

## (1) Issue a Task in Jira

In most cases, task content is described in Jira.
Basically, there are no restrictions on task content or format to be described in Jira, but it's good to describe specifically. Especially describing "Figma URL" is convenient for integration with Figma MCP during development.

The following shows the basic format:

```
Background & Purpose
・xxx
AC (Acceptance Criteria)
・xxx
Likely Tasks
・xxx
Not to Do
・xxx
Reference
・Figma URL：
```

## (2) Transfer Implementation Specifications from Jira to Github Issue

Use Claude Code Command to transfer content described in Jira to Github Issue.

### Reasons for Using Github Issues

#### Reason (0): Jira MCP is unstable

- In some environments, errors may occur when using Jira MCP.
- The author encountered this several times, and while it resolves over time, instability is felt.

#### **Reason (1): Claude Code has the functionality to read Issue content and create PRs**

- Let's actively utilize this functionality.

[Claude Code GitHub Actions - Anthropic](https://docs.anthropic.com/ja/docs/claude-code/github-actions)

#### **Reason (2): Role separation - Jira for immediate task creation | Github Issue as development specification document**

- Use Jira as a place to casually write tasks.
- Use Github Issue as a place to describe development specifications including code context.

#### Reason (3): To serve as context for code generation

- We felt it was risky for AI to directly write to Jira.
- PM writes specifications in Jira → Programmer expands specifications using AI → If this expansion target is Jira, problems may occur when previous information is lost or unintended output occurs.
  - With Issues, it's a new data source, so flexible modification or deletion is possible as needed.

#### Reason (4): To visualize to other members and enable review

- By inserting a development planning stage in Issues before tackling complex tasks, we achieve safe and fast development.

### Create a Command for Claude Code to Create Github Issues

Add the following to .claude/commands/create-issue.md:

```
## Create Issue

The ultimate goal is to create an Issue in the specified Github repository using `github mcp` based on information given by the user.
The Issue title should clearly describe the task title.
The Issue description should output task details adhering to the specified output format.

### Task Progression
(0) Identify the project's Github repository and owner name using MCP. Ask the user if unclear.
(1) Understand the information given by the user.
(2) Check README.md and code to confirm current implementation.
(3) Determine whether the given information can be divided into multiple executable tasks.
(4) Propose multiple Github Issues if division is better, single if it should be single.
(5) Ask the user to confirm the content "before" outputting to Github Issue.
(6) If approval is obtained, create Github Issue with the previously obtained owner and repository name.

### Important Notes

- If the owner/repository to create is unclear, ask the user.
- Since the purpose is to create Issues, code changes/creation is prohibited.

### Output Format Example: Github Issue Description

[Background]
As part of the XXX task, YYY needs to be implemented.
This task will properly design ZZZ and implement it following the project's naming conventions.

[Preparation]
- Refer to `xxx` (filename)
- Refer to existing implementation in `xxx` (filename)

[To Do]
- Add definition to xxx
- Comply with function/variable name xxx format
- Create response in `XXX` format
- Execute `xxx` after changes to confirm implementation

[Not To Do]
- Do not add new definitions to xxx

[Completion Checklist]
- Execute XXX and confirm no errors

[Reference]
- Figma URL (optional)：
- Jira URL (optional)：
```

### Usage

Open claude and execute the command as follows:

```
> /create-issue {Paste Jira content or task overview here}
```

This will generate development specifications from Jira content and create Issues. An example Issue is below.

![Screenshot 2025-07-06 0 34 47](https://github.com/user-attachments/assets/097aadc6-9c89-4899-a50b-84024994ab09)

If the content differs from intention, have Claude Code modify it or edit the Issue yourself.

## (3) Pair Programming with Claude Code | Leave it to Claude Code Actions

### Use Claude Code to execute tasks in an orchestrated manner

This setting allows complex tasks to be analyzed and decomposed step by step, with parallel execution of subtasks. You can review results at each step and flexibly adjust plans.

Edit .claude/commands/orchestrator.md as follows:

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

### For Pair Programming

Use Claude Code to read Issue information via Github MCP and proceed with implementation. Launch Claude and pass the Issue URL for implementation.

```
> /orchestrator Please proceed with implementation of https://github.com/ZenTech-AI/ai-dev-tools/issues/4
```

### When Using Claude Code Actions

Input the following in comments of the created Issue and execute on Github Actions.

```
@claude Implement and create PR.
```

## (4) Create Branch, git add & commit, and Create PR

Use Claude Command to create branch, git add code changes, commit, and create PR in one go.

Add the following to .claude/commands/git-add-commit-create-pr.md:

```
## Git add & commit & create PR

The ultimate goal is to create a PR in the specified Github repository using `github mcp` based on information given by the user.
The PR title should clearly describe the implementation overview.
The PR description should output implementation details adhering to the specified output format.

### Task Progression
(0) Identify the project's Github repository and owner name using MCP. Ask the user if unclear.
(1) Use git status and git diff to confirm implementation.
(2) Plan to commit at appropriate granularity.
(3) Create branch, git add & git commit, and create PR.

### Important Notes

- If the owner/repository to create is unclear, ask the user.
- Since the purpose is to create PRs, code changes/creation is prohibited.

### Output Format Example: Github PR Description

[Related Issue]
{If there's a related Issue, describe it here.}

[Overview]
Implemented YYY as part of the XXX task.
This implementation adds ZZZ functionality, enhancing extensibility and maintainability.

[Cause and Solution] (for bug fixes)
{What was determined as the cause and how it was addressed}

[What Was Done]
{Briefly list the actions taken}

[Results of Changes]
{Operation videos, screenshots, response content, etc.}

[Not Done]
{Content that is out of scope for this PR}

[Notes]
{Content that members should know, such as commands to run after merging}

[Issues]
{Areas of concern, particularly areas that need review (this can also be commented directly in the source)}

[Remarks]
{Other additional items, related materials, reference materials, etc.}
```

The following command checks current implementation and handles commit at appropriate granularity and PR creation:

```
> /git-add-commit-create-pr
```

## (5) Human Review & Claude Code Review

※This might be better set when the repository is created.

Configure claude.yml as follows:

```
// claude.yml

// ...(omitted)

- name: Run Claude Code
  id: claude
  uses: anthropics/claude-code-action@beta
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    custom_instructions: |
      - Review perspectives are in @docs/REVIEW.md. Please refer to this and ultrathink to ensure absolutely no omissions.
      - Always reply in Japanese.
```

Next, describe review perspectives in docs/README.md:

```
# Code Review Guidelines
Using LLM to review **code generated by AI and new members**, with the purpose of:
- **Preventing destructive changes and degradation**
- **Early detection of bugs and security flaws**
- **Maintaining/improving code quality with high maintainability and safety**
LLM will receive prompts including this guideline, and comments must be labeled with **"MUST / WANT / FYI / NITS"**.

## Review Points

> ▶ Listing check items under each perspective with *MUST / WANT / FYI / NITS* makes it easier for LLM to return priority-based comments automatically.

### 1. Specification & Functional Validity
- **MUST**: Do code behaviors match specifications and user stories?
- **WANT**: Is the design non-interfering with additional specifications and future extensions?
- **FYI**: Are there existing modules or standard libraries with similar functionality?

### 2. Compatibility & Destructive Change Prevention
- **MUST**: Are there no changes to public API/data model signatures?
- **NITS**: Changes in import order due to refactoring, etc.

### 3. Security
- **MUST**: Are there countermeasures against known vulnerabilities like injection, XSS, CSRF?
- **MUST**: Are secret information (API keys, passwords) not hardcoded?
- **WANT**: Are permission boundaries clearly separated (Zero-Trust concept)?

### 4. Readability & Maintainability
- **MUST**: Is naming consistent and expressing intent?
- **MUST**: Does it not contain anti-patterns like circular dependencies or giant classes?
- **WANT**: Are functions/classes divided into single responsibilities that are easy to test?
- **NITS**: Language unification in comments, newlines, indentation, spacing, and other style details.

### 5. Error Handling & Logging
- **MUST**: Are exceptions properly propagated/handled without being suppressed?
- **FYI**: Existing log infrastructure format and level guidelines.

### 6. Documentation
- **NITS**: Indentation and comment notation variations in sample code.
```