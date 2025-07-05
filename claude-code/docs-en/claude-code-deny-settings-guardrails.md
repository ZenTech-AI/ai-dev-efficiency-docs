# Claude Code Deny Settings as Guardrails

## Overview

This document explains how to configure deny settings in Claude Code to restrict dangerous commands and tools, creating a safe development environment.

## Features

- Prohibit execution of specific commands
- Restrict tool usage
- Improve system safety
- Prevent unintended operations

## Configuration Method

### Basic Configuration

Edit the `~/.claude/settings.json` file as follows:

```json
{
  "permissions": {
    "allow": ["Bash(play:*)"],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(rm -rf ~)",
      "Bash(rm -rf ~/*)",
      "Bash(rm -rf /*)"
    ]
  }
}
```

### Configuration Details

#### `allow` Section

- **Purpose**: Specify commands and tools explicitly allowed
- **Example**: `"Bash(play:*)"` - Allow use of play command
- **Pattern**: Flexible specification using wildcards (*) is possible

#### `deny` Section

- **Purpose**: Specify commands and tools to prohibit
- **Example**: `"Bash(rm -rf /)"` - Prohibit system-wide deletion
- **Effect**: Completely block execution of specified commands

## Practical Configuration Examples

### Security-Focused Configuration

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Write(*)",
      "Bash(git *)",
      "Bash(npm *)",
      "Bash(yarn *)",
      "Bash(play:*)"
    ],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(rm -rf ~)",
      "Bash(rm -rf ~/*)",
      "Bash(rm -rf /*)",
      "Bash(sudo *)",
      "Bash(chmod 777 *)",
      "Bash(curl * | bash)",
      "Bash(wget * | bash)"
    ]
  }
}
```

### Development Environment Specific Configuration

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Write(*)",
      "Edit(*)",
      "Bash(git *)",
      "Bash(npm *)",
      "Bash(node *)",
      "Bash(python *)",
      "Bash(pip *)",
      "Bash(docker *)",
      "Bash(play:*)"
    ],
    "deny": [
      "Bash(rm -rf /)",
      "Bash(rm -rf ~)",
      "Bash(format *)",
      "Bash(fdisk *)",
      "Bash(mkfs *)",
      "WebFetch(*suspicious-domain*)"
    ]
  }
}
```

## Recommended Prohibited Commands

### System Destructive Commands

```json
"deny": [
  "Bash(rm -rf /)",
  "Bash(rm -rf ~)",
  "Bash(rm -rf ~/*)",
  "Bash(rm -rf /*)",
  "Bash(format *)",
  "Bash(fdisk *)",
  "Bash(mkfs *)"
]
```

### Privilege Escalation Commands

```json
"deny": [
  "Bash(sudo *)",
  "Bash(su *)",
  "Bash(chmod 777 *)",
  "Bash(chown root *)"
]
```

### Network Commands

```json
"deny": [
  "Bash(curl * | bash)",
  "Bash(wget * | bash)",
  "Bash(nc -l *)",
  "WebFetch(*suspicious-domain*)"
]
```

## Best Practices

### 1. Gradual Restrictions

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Write(./src/*)",
      "Bash(git *)",
      "Bash(npm run *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Write(/etc/*)",
      "Write(/usr/*)"
    ]
  }
}
```

### 2. Project-Specific Configuration

```json
{
  "permissions": {
    "allow": [
      "Read(./project/*)",
      "Write(./project/src/*)",
      "Bash(cd ./project && *)"
    ],
    "deny": [
      "Read(~/.ssh/*)",
      "Write(../*)",
      "Bash(cd .. && *)"
    ]
  }
}
```

### 3. Development Phase-Specific Restrictions

#### Development Phase

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Write(*)",
      "Bash(npm *)",
      "Bash(git *)"
    ],
    "deny": [
      "Bash(npm publish *)",
      "Bash(docker push *)"
    ]
  }
}
```

#### Production Deployment Phase

```json
{
  "permissions": {
    "allow": [
      "Read(*)",
      "Bash(git *)",
      "Bash(docker build *)",
      "Bash(docker push *)"
    ],
    "deny": [
      "Write(*)",
      "Bash(rm *)",
      "Bash(npm install *)"
    ]
  }
}
```

## Configuration Verification and Testing

### Configuration Check Commands

```bash
# Check configuration file contents
cat ~/.claude/settings.json

# Verify JSON format validity
python -m json.tool ~/.claude/settings.json
```

### Testing Procedures

1. **Test Prohibited Commands**
   ```
   # Try in Claude Code
   rm -rf /tmp/test
   ```

2. **Test Allowed Commands**
   ```
   # Try in Claude Code
   git status
   ```

3. **Test Wildcards**
   ```
   # Try in Claude Code
   play -n synth 0.1 sine 440
   ```

## Notes and Troubleshooting

### Configuration Precautions

- **JSON Format Accuracy**: Be careful of syntax errors
- **Path Specification**: Proper use of relative and absolute paths
- **Wildcards**: Avoid overly broad restrictions

### Common Issues

#### 1. Configuration Not Applied

```bash
# Restart Claude Code
claude --restart
```

#### 2. Necessary Commands Prohibited

```json
{
  "permissions": {
    "allow": [
      "Bash(rm ./tmp/*)"  // Allow only specific directory
    ],
    "deny": [
      "Bash(rm -rf /)"    // Prohibit root deletion
    ]
  }
}
```

## Security Benefits

### Risk Mitigation

- **System Destruction Prevention**: Prevent unintended deletion of system files
- **Data Protection**: Prevent accidental deletion of important files
- **Privilege Escalation Prevention**: Prevent unauthorized permission changes

### Development Efficiency Improvement

- **Peace of Mind**: Development in a safe environment
- **Standardization**: Unified security policy across teams
- **Automation**: Reduced manual checking

## References

- [Claude Code Deny Settings Detailed Guide](https://izanami.dev/post/d6f25eec-71aa-4746-8c0d-80c67a1459be)
- [Claude Code Settings Documentation](https://docs.anthropic.com/en/docs/claude-code/settings)
- [Claude Code Security Best Practices](https://docs.anthropic.com/en/docs/claude-code/security)