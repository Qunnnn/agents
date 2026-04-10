---
tags: [prompt, code-review]
applies-to: [antigravity, cursor, copilot]
---

# Code Review Prompts

## Quick Review

```
Review this code for:
1. Bugs and logical errors
2. Performance issues
3. Security vulnerabilities
4. Code style violations
5. Missing error handling

Be concise. List issues with severity (🔴 critical, 🟡 warning, 🟢 suggestion).
```

## Architecture Review

```
Review this code's architecture:
1. Does it follow SOLID principles?
2. Is the separation of concerns correct?
3. Are dependencies properly injected?
4. Is the code testable?
5. Are there any circular dependencies?

Suggest improvements with code examples.
```

## PR Review

```
Review this pull request:
1. Does it do what the PR description says?
2. Are there any unintended side effects?
3. Is the change backward compatible?
4. Are all edge cases handled?
5. Is there adequate test coverage?

Format: table with columns [File, Line, Issue, Severity, Suggestion]
```
