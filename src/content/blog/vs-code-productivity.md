---
title: 'VS Code Productivity Hacks: Code Faster'
description: 'Master Visual Studio Code with these essential shortcuts, extensions, and tips for maximum productivity'
pubDate: 'Dec 16 2025'
heroImage: '../../assets/blog-placeholder-1.jpg'
---

VS Code is the most popular code editor for good reason - it's fast, extensible, and packed with features. Learn how to use it like a pro and boost your productivity.

## Essential Keyboard Shortcuts

### Navigation
- `Cmd/Ctrl + P` - Quick file open
- `Cmd/Ctrl + Shift + P` - Command palette
- `Cmd/Ctrl + B` - Toggle sidebar
- `Cmd/Ctrl + \`` - Toggle terminal
- `Ctrl + Tab` - Switch between files
- `Cmd/Ctrl + W` - Close file

### Editing
- `Cmd/Ctrl + D` - Select next occurrence
- `Cmd/Ctrl + Shift + L` - Select all occurrences
- `Alt + Up/Down` - Move line up/down
- `Shift + Alt + Up/Down` - Copy line up/down
- `Cmd/Ctrl + /` - Toggle comment
- `Cmd/Ctrl + Shift + K` - Delete line

### Multi-cursor
- `Alt + Click` - Add cursor
- `Cmd/Ctrl + Alt + Up/Down` - Add cursor above/below
- `Cmd/Ctrl + U` - Undo last cursor operation

## Must-Have Extensions

### General
- **Prettier** - Code formatter
- **ESLint** - JavaScript linter
- **GitLens** - Supercharged Git
- **Auto Rename Tag** - Sync HTML tag pairs
- **Path Intellisense** - Autocomplete file paths
- **Error Lens** - Show errors inline

### Language-Specific
- **Python** - Python support
- **Pylance** - Python language server
- **vscode-icons** - File icons
- **Tailwind CSS IntelliSense** - Tailwind autocomplete

### Productivity
- **Live Share** - Collaborative editing
- **Rest Client** - Test APIs in VS Code
- **TODO Highlight** - Highlight TODOs
- **Better Comments** - Colorful comments

## Customization

### Settings.json

\`\`\`json
{
  "editor.fontSize": 14,
  "editor.fontFamily": "Fira Code, Menlo, Monaco",
  "editor.fontLigatures": true,
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.minimap.enabled": false,
  "editor.lineHeight": 1.6,
  "files.autoSave": "afterDelay",
  "terminal.integrated.fontSize": 13,
  "workbench.colorTheme": "GitHub Dark",
  "workbench.iconTheme": "vscode-icons"
}
\`\`\`

### Custom Snippets

Create custom snippets for repeated code:

\`\`\`json
{
  "React Component": {
    "prefix": "rfc",
    "body": [
      "export default function ${1:ComponentName}() {",
      "  return (",
      "    <div>",
      "      $0",
      "    </div>",
      "  );",
      "}"
    ]
  }
}
\`\`\`

## Advanced Features

### Multi-File Search & Replace
1. `Cmd/Ctrl + Shift + F` - Search across files
2. `Cmd/Ctrl + Shift + H` - Replace across files
3. Use regex for power: `function\\s+(\\w+)`

### Refactoring
- `F2` - Rename symbol
- `Cmd/Ctrl + .` - Quick fix
- Right-click → Refactor

### Debugging
1. Set breakpoint (click line number)
2. Press `F5` to start debugging
3. Use Debug Console for REPL

Master VS Code and code 10x faster!
