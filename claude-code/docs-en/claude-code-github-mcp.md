# Claude Code × Github MCP Integration Guide

## Overview

Integrating Claude Code with Github MCP enables direct GitHub repository operations and issue management within Claude Code.

## Features

- Direct GitHub repository operations
- Issue and Pull Request creation and management
- Repository information retrieval and search
- Commit and branch management

## Setup Instructions

### 1. Attempt MCP Connection (May Result in Error)

```bash
claude mcp add --transport sse github-server https://api.github.com/mcp
```

### 2. Personal Access Token (PAT) Connection

If the above command fails, use PAT-based connection.

#### Generate GitHub PAT

1. Navigate to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Select required permissions:
   - `repo`: Full repository access (recommended)
   - Additional permissions as needed
4. Generate and save the token

#### Configure MCP Connection

```bash
export GITHUB_PAT="github_pat_XXX"
claude mcp add -s user --transport http github-server https://api.githubcopilot.com/mcp -H "Authorization: Bearer $GITHUB_PAT"
```

## Claude Code MCP Scopes

### Local Scope (`local`) [Default]

- Personal settings within a project
- Suitable for sensitive information or experimental configurations

```bash
claude mcp add my-server /path/to/server
claude mcp add -s local my-server /path/to/server
```

### Project Scope (`project`)

- Team-shared settings
- Stored in `.mcp.json` file, included in version control

```bash
claude mcp add -s project shared-server /path/to/server
```

### User Scope (`user`)

- Personal settings for all projects

```bash
claude mcp add -s user my-global-server /path/to/server
```

## Usage Examples

### Creating Issues

```
Create an issue in the XX repository with content YY
```

### Creating Pull Requests

```
Create a Pull Request in the XX repository with title YY and content ZZ
```

### Retrieving Repository Information

```
Show me the latest commit information for the XX repository
```

## References

- [Claude Code MCP Official Documentation](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [Claude Code Remote MCP Usage Guide](https://dev.classmethod.jp/articles/shuntaka-support-claude-code-remote-mcp/)
- [GitHub Personal Access Tokens Setup Guide](https://dev.classmethod.jp/articles/github-personal-access-tokens/)