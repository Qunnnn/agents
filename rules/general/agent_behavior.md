---
tags: [general, ai-agent, interaction]
applies-to: [antigravity, cursor, copilot]
---

# General AI Agent Rules

## Communication

- Be concise. Avoid lengthy explanations unless asked.
- Summarize changes after completing a task.
- Ask for clarification when requirements are ambiguous.
- Don't repeat file contents back unless specifically asked.

## Code Changes

- Preserve existing comments and documentation unrelated to the change.
- Don't introduce unnecessary refactoring beyond the requested scope.
- Follow existing code style and patterns in the project.
- When modifying a file, understand its full context first.

## Safety

- Never delete files without explicit confirmation.
- Never modify `.env`, secrets, or credential files without asking.
- Always show destructive commands for approval before execution.
- Back up complex changes by explaining what will be modified first.

## Quality

- Write code that matches the quality bar of the existing codebase.
- Include error handling for edge cases.
- Ensure new code is consistent with existing patterns.
- Consider cross-platform implications when relevant.

## Dependencies

- Don't add new dependencies without discussing alternatives.
- Prefer well-maintained, popular packages.
- Check for version compatibility before suggesting packages.
