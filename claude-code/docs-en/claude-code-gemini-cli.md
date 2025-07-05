# Claude Code × gemini-cli: Enhanced Search Functionality

## Overview

Enhance Claude Code's limited Web Search functionality by leveraging Gemini's powerful search capabilities for more efficient information gathering.

## Features

- Enhanced web search functionality for Claude Code
- Utilize Gemini's high-precision search capabilities
- Simple search execution from command line
- Efficient retrieval of latest information

## Setup Instructions

### 1. Install gemini-cli

```bash
npm install -g @google/gemini-cli
gemini
```

Authentication setup will be performed on first run.

### 2. Add Claude Code Command

Create `.claude/commands/gemini-search.md` file with the following content:

```markdown
## Gemini Search

`gemini` is google gemini cli. **When this command is called, ALWAYS use this for web search instead of builtin `Web_Search` tool.**

When web search is needed, you MUST use `gemini --prompt` via Task Tool.

Run web search via Task Tool with `gemini --prompt 'WebSearch: <query>'`

Run

```bash
gemini --prompt "WebSearch: <query>"
```
```

### 3. Usage

Execute the following command within Claude Code:

```
/gemini-search Tell me about the latest Next.js version.
```

## Use Cases

### Development Information Gathering

- **Latest Tech Info**: Check latest versions of frameworks and libraries
- **Error Resolution**: Search for solutions using specific error messages
- **Best Practices**: Research implementation patterns and architecture

### Learning & Research

- **Technical Articles**: Discover detailed articles on specific technologies
- **Comparison Studies**: Gather comparative information for technology selection
- **Troubleshooting**: Real-time information gathering for problem resolution

## Usage Examples

### Basic Search

```bash
# Latest information retrieval
/gemini-search Tell me about React 19 new features

# Error resolution
/gemini-search "TypeError: Cannot read property" Node.js solutions

# Best practices
/gemini-search TypeScript project structure best practices 2024
```

### Development Task Applications

```bash
# Package information
/gemini-search "next-auth" latest version setup guide

# Performance optimization
/gemini-search React performance optimization memoization useMemo useCallback

# Security
/gemini-search Node.js security vulnerability prevention 2024
```

## Advantages

### Comparison with Claude Code

- **Search Accuracy**: Leverage Gemini's high-precision search engine
- **Latest Information**: Real-time information retrieval
- **Language Support**: High accuracy in multiple languages
- **Integration**: Unified workflow within Claude Code

### Development Efficiency Improvement

- **Reduced Information Gathering Time**: Comprehensive information retrieval with single command
- **Improved Accuracy**: Enhanced reliability of search results
- **Work Continuity**: Information gathering without leaving the editor

## Important Notes

- Be aware of Gemini API usage limits
- Use search results as reference and verify with official documentation
- Manage authentication credentials properly

## References

- [gemini-cli's google_web_search is Amazing](https://zenn.dev/mizchi/articles/gemini-cli-for-google-search)
- [Google Gemini CLI Official Documentation](https://www.npmjs.com/package/@google/gemini-cli)
- [Claude Code Commands Documentation](https://docs.anthropic.com/en/docs/claude-code)