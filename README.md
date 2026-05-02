# 🤖 AI Agent Skills & Rules

> A personal knowledge base of reusable skills, rules, and configurations for AI coding assistants.

## 📁 Structure

```
.
├── skills/                    # Reusable skill instructions
│   ├── dart/                  # Dart core skills (from dart-lang/skills)
│   ├── flutter/               # Flutter-specific skills
│   ├── go/                    # Go/Backend specific skills
│   ├── general/               # Language-agnostic skills
│   └── devops/                # CI/CD, Docker, deployment skills
│
├── rules/                     # Project-level rules & conventions
│   ├── flutter/               # Flutter project rules
│   ├── go/                    # Go project rules
│   └── general/               # General coding rules
│
├── templates/                 # Ready-to-use config templates
│   ├── antigravity/           # Antigravity (.agents/) templates
│   ├── cursor/                # Cursor (.cursorrules) templates
│   ├── copilot/               # GitHub Copilot templates
│   └── gemini/                # Gemini (.gemini/) templates
│
├── prompts/                   # Reusable prompt snippets
│   ├── code-review.md         # Code review prompts
│   ├── refactoring.md         # Refactoring prompts
│   └── debugging.md           # Debugging prompts
│
└── README.md
```

## 🚀 Quick Start

### Using Skills

Each skill is a standalone markdown file that can be:
- Copied directly into your project's `.agents/skills/` folder (Antigravity)
- Referenced in your `.cursorrules` or `AGENTS.md`
- Used as context when chatting with any AI assistant

### Using Rules

Rules define project-wide conventions. Drop them into your project's agent config:

```bash
# For Antigravity — copy rules to your project
cp rules/flutter/architecture.md /your-project/.agents/rules/

# For Cursor — append to .cursorrules
cat rules/flutter/architecture.md >> /your-project/.cursorrules
```

### Using Templates

Templates are ready-to-use configuration files:

```bash
# Copy Antigravity template to a new Flutter project
cp -r templates/antigravity/flutter/ /your-project/.agents/
```

## 📝 Skill Format

Each skill follows this structure:

```markdown
# Skill Name

## Context
When to apply this skill.

## Instructions
Step-by-step instructions for the AI agent.

## Examples
Concrete examples of input → output.

## Anti-patterns
What to avoid.
```

## 🏷️ Tags

Skills and rules use front-matter tags for easy discovery:

```markdown
---
tags: [flutter, bloc, state-management]
applies-to: [antigravity, cursor, copilot]
level: [project, file, snippet]
---
```

## 📄 License

Personal use. Feel free to fork and customize for your own workflow.

## 🤝 Credits & Resources

- [dart-lang/skills](https://github.com/dart-lang/skills) - Official Dart Agent Skills maintained by the Dart team.
