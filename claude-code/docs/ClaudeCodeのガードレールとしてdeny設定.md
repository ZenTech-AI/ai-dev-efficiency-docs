# Claude Code のガードレールとしてdeny設定

## 概要

Claude Codeで危険なコマンドやツールの使用を制限し、安全な開発環境を構築するためのdeny設定について説明します。

## 機能

- 特定のコマンドの実行を禁止
- ツールの使用制限
- システムの安全性向上
- 意図しない操作の防止

## 設定方法

### 基本設定

`~/.claude/settings.json` ファイルを以下のように編集：

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

### 設定の詳細

#### `allow` セクション

- **目的**: 明示的に許可するコマンドやツールを指定
- **例**: `"Bash(play:*)"` - playコマンドの使用を許可
- **パターン**: ワイルドカード（*）を使用した柔軟な指定が可能

#### `deny` セクション

- **目的**: 禁止するコマンドやツールを指定
- **例**: `"Bash(rm -rf /)"` - システム全体の削除を禁止
- **効果**: 指定されたコマンドの実行を完全にブロック

## 実用的な設定例

### セキュリティ重視の設定

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

### 開発環境特化の設定

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

## 推奨する禁止コマンド

### システム破壊系

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

### 権限昇格系

```json
"deny": [
  "Bash(sudo *)",
  "Bash(su *)",
  "Bash(chmod 777 *)",
  "Bash(chown root *)"
]
```

### ネットワーク系

```json
"deny": [
  "Bash(curl * | bash)",
  "Bash(wget * | bash)",
  "Bash(nc -l *)",
  "WebFetch(*suspicious-domain*)"
]
```

## ベストプラクティス

### 1. 段階的な制限

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

### 2. プロジェクト別の設定

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

### 3. 開発フェーズ別の制限

#### 開発フェーズ

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

#### 本番デプロイフェーズ

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

## 設定の確認とテスト

### 設定確認コマンド

```bash
# 設定ファイルの内容確認
cat ~/.claude/settings.json

# JSON形式の妥当性確認
python -m json.tool ~/.claude/settings.json
```

### テスト手順

1. **禁止コマンドのテスト**
   ```
   # Claude Codeで以下を試す
   rm -rf /tmp/test
   ```

2. **許可コマンドのテスト**
   ```
   # Claude Codeで以下を試す
   git status
   ```

3. **ワイルドカードのテスト**
   ```
   # Claude Codeで以下を試す
   play -n synth 0.1 sine 440
   ```

## 注意点とトラブルシューティング

### 設定時の注意

- **JSON形式の正確性**: 構文エラーに注意
- **パス指定**: 相対パスと絶対パスの使い分け
- **ワイルドカード**: 過度に広範囲な制限は避ける

### よくある問題

#### 1. 設定が反映されない

```bash
# Claude Codeの再起動
claude --restart
```

#### 2. 必要なコマンドが禁止される

```json
{
  "permissions": {
    "allow": [
      "Bash(rm ./tmp/*)"  // 特定のディレクトリのみ許可
    ],
    "deny": [
      "Bash(rm -rf /)"    // ルート削除は禁止
    ]
  }
}
```

## セキュリティ上の利点

### リスク軽減

- **システム破壊の防止**: 意図しないシステムファイルの削除防止
- **データ保護**: 重要なファイルの誤削除防止
- **権限昇格の阻止**: 不正な権限変更の防止

### 開発効率の向上

- **安心感**: 安全な環境での開発
- **標準化**: チーム全体での統一されたセキュリティポリシー
- **自動化**: 手動チェックの削減

## 参考資料

- [Claude Code Deny設定詳細ガイド](https://izanami.dev/post/d6f25eec-71aa-4746-8c0d-80c67a1459be)
- [Claude Code Settings Documentation](https://docs.anthropic.com/en/docs/claude-code/settings)
- [Claude Code Security Best Practices](https://docs.anthropic.com/en/docs/claude-code/security)