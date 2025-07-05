# Claude Code: Solving Instruction Forgetting Issues

## Overview

Claude Code can sometimes forget initial instructions or rules during long sessions or after many exchanges. This document explains effective methods to resolve this issue.

## Problem Background

- Context dilution during long sessions
- Initial instructions forgotten after many exchanges
- Important rules and constraints not being followed
- Loss of work consistency

## Solution

### Basic Strategy

**Maintain context by having instructions output every time**

This method ensures AI remembers important instructions in each exchange and maintains consistent behavior.

### Setup Instructions

Edit the `~/.claude/CLAUDE.md` file as follows:

```markdown
<language>Japanese</language>
<character_code>UTF-8</character_code>
<law>
AI Operation 5 Principles

Principle 1: AI must report its work plan before file generation/update/program execution, get y/n user confirmation, and stop all execution until y is returned.

Principle 2: AI must not perform detours or alternative approaches independently; if the initial plan fails, get confirmation for the next plan.

Principle 3: AI is a tool and decision-making authority always lies with the user. Even if user suggestions are inefficient or irrational, do not optimize and execute as instructed.

Principle 4: AI must not distort or reinterpret these rules; they must be absolutely observed as top-level commands.

Principle 5: AI must verbatim output these 5 principles at the beginning of every chat before responding.
</law>

<every_chat>
[AI Operation 5 Principles]

[main_output]

#[n] times. # n = increment each chat, end line, etc(#1, #2...)
</every_chat>
```

## Configuration Details

### `<law>` Section

- **Role**: Define rules that must be absolutely followed
- **Characteristics**: Functions as top-level commands
- **Effect**: Strictly control AI behavior

### `<every_chat>` Section

- **Role**: Processing that must be executed in every exchange
- **Characteristics**: Context maintenance functionality
- **Effect**: Prevention of instruction forgetting

### Numbering System

```
#[n] times. # n = increment each chat, end line, etc(#1, #2...)
```

- **Purpose**: Track the number of exchanges
- **Benefits**: Understand session progress
- **Effect**: Ensure consistency

## Operational Best Practices

### 1. Clear Important Rules

```markdown
<law>
[List rules that must be absolutely followed here]
</law>
```

### 2. Regular Check Items

```markdown
<every_chat>
[List items to check every time]
</every_chat>
```

### 3. Staged Instructions

- **Stage 1**: Set basic rules
- **Stage 2**: Define specific work flows
- **Stage 3**: Clarify exception handling

## Usage Examples

### Development Project Management

```markdown
<law>
Development 5 Principles
Principle 1: Never write code without tests
Principle 2: Prioritize security above all
Principle 3: Always consider performance
Principle 4: Emphasize readability
Principle 5: Always update documentation
</law>
```

### Code Review

```markdown
<every_chat>
[Checkpoints]
- Security vulnerability check
- Performance verification
- Test coverage confirmation
</every_chat>
```

## Important Notes

### Setup Considerations

- **Keep it simple**: Overly complex settings are counterproductive
- **Clear expression**: Avoid ambiguous instructions
- **Test execution**: Always verify operation after setup

### Operational Considerations

- **Regular review**: Periodically evaluate setting effectiveness
- **Appropriate adjustment**: Fine-tune settings as needed
- **Feedback**: Continuously collect improvement points

## Effects and Benefits

### Short-term Effects

- **Improved consistency**: Operate with same standards every time
- **Reduced errors**: Prevent oversight of important rules
- **Improved efficiency**: Eliminate need for re-explanation

### Long-term Effects

- **Stable quality**: Continuous quality assurance
- **Learning effect**: Improved AI behavior patterns
- **Improved reliability**: Predictable operation

## References

- [Found a Super Effective Method to Solve Claude Code's "Quickly Forget Rules Problem"](https://zenn.dev/sesere/articles/0420ecec9526dc)
- [Claude Code Memory Management](https://docs.anthropic.com/en/docs/claude-code/memory)
- [Claude Code Configuration Guide](https://docs.anthropic.com/en/docs/claude-code/settings)