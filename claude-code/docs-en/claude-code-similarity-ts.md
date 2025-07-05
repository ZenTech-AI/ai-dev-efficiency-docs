# Claude Code × similarity-ts: Duplicate Code Detection Guide

## Overview

This guide explains how to use similarity-ts to detect duplicate code in TypeScript/Python projects and efficiently perform refactoring with Claude Code.

## Features

- Automatic detection of duplicate code in TypeScript/Python
- Discovery of semantically similar code patterns
- Refactoring assistance through Claude Code integration
- Multi-language support (TypeScript, Python)

## Setup Instructions

### 1. Install similarity-ts

Install via Rust's cargo:

```bash
cargo install similarity-ts
cargo install similarity-py
```

### 2. Usage

Run from the project root directory:

```bash
# For TypeScript projects
similarity-ts

# For Python projects
similarity-py
```

### 3. Detailed Options

Check available options:

```bash
similarity-ts -h
```

## Integration with Claude Code

### Duplicate Code Detection and Refactoring

Example usage with Claude Code:

```
Run `similarity-ts .` to detect semantic code similarities. Execute this command, analyze the duplicate code patterns, and create a refactoring plan. Check `similarity-ts -h` for detailed options.
```

### Execution Steps

1. **Duplicate Code Detection**
   ```bash
   similarity-ts .
   ```

2. **Result Analysis**
   - Review detected duplicate code patterns
   - Evaluate similarity scores
   - Determine refactoring priorities

3. **Create Refactoring Plan**
   - Identify logic that can be shared
   - Plan function/class extraction
   - Analyze impact scope

4. **Implementation with Claude Code**
   - Execute refactoring based on detection results
   - Create and run test cases
   - Perform code review

## Use Cases

### Development Efficiency

- **Code Review**: Detect duplicate code in PRs
- **Refactoring**: Resolve technical debt
- **New Feature Development**: Check similarity with existing code

### Quality Improvement

- **Maintainability**: Reduce duplicate code
- **Bug Fix Efficiency**: Batch fix similar bugs
- **Code Consistency**: Enforce coding standards across teams

## Important Notes

- Execution time may be long for large projects
- Use detection results as reference; final decisions should be made by humans
- Always run tests before refactoring

## References

- [Finding Duplicate Code with similarity-ts](https://zenn.dev/mizchi/articles/introduce-ts-similarity)
- [similarity-ts GitHub Repository](https://github.com/mizchi/similarity-ts)
- [Claude Code Official Documentation](https://docs.anthropic.com/en/docs/claude-code)