---
tags: [meta, workflow, skill-discovery]
applies-to: [antigravity, cursor, copilot, gemini]
level: project
---

# Using Skills

## Context

Use at the start of any conversation or task. This skill establishes how to discover, load, and apply the right skills from this repository before taking any action.

## The Rule

**Check for relevant skills BEFORE any response or action.** If there's even a small chance a skill applies to your current task, read and follow it. If the skill turns out to be irrelevant, move on — but always check first.

## Instruction Priority

When instructions conflict, follow this precedence:

1. **User's explicit instructions** (AGENTS.md, GEMINI.md, `.cursorrules`, direct requests) — highest priority
2. **Skills from this repository** — override default AI behavior where they apply
3. **Default AI system prompt** — lowest priority

## How to Find Skills

### Skill Registry

Skills are organized by domain under `skills/`:

| Category | Path | Skills |
|----------|------|--------|
| **Flutter** | `skills/flutter/` | `bloc-pattern`, `concurrency`, `http-and-json`, `localization`, `navigation`, `project-structure`, `testing`, `theming` |
| **Go** | `skills/go/` | `api-handler` |
| **General** | `skills/general/` | `error-handling`, `using-skills` |
| **DevOps** | `skills/devops/` | `docker` |

### Matching Skills to Tasks

```
User wants to...              → Check skill
────────────────────────────────────────────────────
Build Flutter UI/feature      → flutter/project-structure, flutter/theming
Add state management          → flutter/bloc-pattern
Make API calls                → flutter/http-and-json
Add translations              → flutter/localization
Set up routing                → flutter/navigation
Write Flutter tests           → flutter/testing
Handle async/isolates         → flutter/concurrency
Write Go API endpoints        → go/api-handler
Handle errors                 → general/error-handling
Containerize a service        → devops/docker
```

### Skill Format

Each skill is a standalone `SKILL.md` with:
- **Context** — When to apply the skill
- **Instructions** — Step-by-step guidance
- **Examples** — Concrete input → output
- **Anti-patterns** — What to avoid

Read the full skill before acting. Don't skim or assume you know the content.

## Workflow

```
1. Receive task
2. Identify which domain(s) the task touches
3. Check the skill registry above for matching skills
4. Read matching SKILL.md files
5. Follow the skill instructions as you work
6. If no skill matches → proceed with your best judgment
```

## Complementary Resources

Beyond skills, this repository also provides:

| Resource | Path | Purpose |
|----------|------|---------|
| **Rules** | `rules/` | Project-wide conventions (Flutter, Go, general) |
| **Prompts** | `prompts/` | Reusable prompt snippets (code-review, debugging, refactoring) |
| **Templates** | `templates/` | Ready-to-use configs (Antigravity, Cursor, Copilot, Gemini) |

Check `rules/` when you need project conventions. Use `prompts/` for structured review/debug workflows.

## Platform Adaptation

Skills are platform-agnostic markdown. To use them in your AI tool:

| Platform | How to Load Skills |
|----------|--------------------|
| **Antigravity** | Copy to `.agents/skills/` in your project |
| **Cursor** | Reference in `.cursorrules` or paste as context |
| **Copilot** | Reference in `AGENTS.md` or include as context |
| **Gemini CLI** | Reference in `GEMINI.md` or activate via `activate_skill` |

## Red Flags

These thoughts mean STOP — you're skipping skills:

| Thought | Reality |
|---------|---------|
| "This is too simple for a skill" | Simple tasks still benefit from consistent patterns |
| "I already know Flutter/Go patterns" | Skills encode THIS project's conventions, not generic ones |
| "Let me just write code first" | Read the skill first — it prevents rework |
| "I'll check skills later" | Skills shape HOW you work. Check before starting |
| "No skill matches exactly" | Partial matches still provide useful patterns |

## Anti-patterns

- ❌ Skipping skill discovery and jumping straight to code
- ❌ Assuming you know a skill's content without re-reading it
- ❌ Ignoring skills because the task "seems simple"
- ❌ Following a skill that contradicts the user's explicit instructions
- ❌ Using skills from one domain (e.g., Flutter) when working in another (e.g., Go) without verifying applicability
