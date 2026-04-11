---
tags: [flutter, rules, guide, adaptation]
applies-to: [antigravity, cursor, copilot, gemini]
---

# Flutter Rules — Adaptation Guide

> How to customize `rules.md` for your specific Flutter project.

## Overview

The `rules.md` file is a **template** with configurable sections marked by
`<!-- PROJECT_CONFIG -->` comments. Each section provides a sensible default and
one or more alternatives. When adapting for a project, you **choose one option
per section** and remove the rest.

---

## Step 1: Copy to Your Project

```bash
# For Antigravity
cp rules/flutter/rules.md /your-project/.agents/rules/flutter_rules.md

# For Cursor
cat rules/flutter/rules.md >> /your-project/.cursorrules

# For Gemini
cp rules/flutter/rules.md /your-project/.gemini/rules/flutter_rules.md
```

---

## Step 2: Configure Each `PROJECT_CONFIG` Section

Search for `<!-- PROJECT_CONFIG -->` comments and make your choice:

| Section | Decision | Common Options |
|---------|----------|----------------|
| **Project Structure** | Layout convention | Clean Architecture, Feature-first, Layer-first |
| **Lint Rules** | Lint package | `flutter_lints`, `very_good_analysis`, `lint` |
| **Architecture** | Pattern | MVVM, Clean Architecture + BLoC, MVC |
| **State Management** | Solution | Built-in, BLoC/Cubit, Riverpod, Provider |
| **Dependency Injection** | Approach | Constructor, `get_it`, `provider`, `riverpod` |
| **Routing** | Package | `go_router`, `auto_route`, built-in Navigator |
| **Serialization** | Package | `json_serializable`, `freezed`, manual |
| **Networking** | Package | `http`, `dio` + `retrofit`, `chopper` |
| **Assertions** | Style | `matchers`, `package:checks` |
| **Mocking** | Package | `mocktail`, `mockito`, manual fakes |
| **Logging** | Approach | `dart:developer`, `logging` package, custom |
| **Error Handling** | Strategy | Result types, exceptions, sealed classes |

---

## Step 3: Remove Unused Alternatives

Delete the "Alternative" subsections that don't apply to your project to keep
the rules file **focused and unambiguous**. AI agents work best with clear,
single-option instructions rather than "choose A or B."

**Before (template):**
```markdown
### Default: json_serializable
...code example...

### Alternative: freezed
...code example...
```

**After (adapted for freezed project):**
```markdown
### Data Models: freezed
...code example...
```

---

## Step 4: Add Project-Specific Context

Append sections for your project's unique patterns:

### Recommended Additions

- **Package name and imports:** e.g., `package:omedic/...`
- **API configuration:** Base URLs, environment variables, `--dart-define`
- **Build flavors:** dev, staging, production setup
- **CI/CD commands:** Build, test, deploy pipelines
- **Design system:** Custom widget library, component naming
- **Asset conventions:** Icon generation, image handling
- **Code generation commands:** Specific `build_runner` tasks
- **Environment setup:** Firebase, Google Maps, deep linking

### Example Addition

```markdown
## Build Flavors

Run the app with the correct flavor and environment:

\`\`\`bash
# Development
flutter run --flavor dev --dart-define-from-file=env/.env.dev

# Staging
flutter run --flavor staging --dart-define-from-file=env/.env.staging

# Production
flutter run --flavor prod --dart-define-from-file=env/.env.prod
\`\`\`
```

---

## Step 5: Validate

After adaptation, verify:

- [ ] No `<!-- PROJECT_CONFIG -->` comments remain (all decisions made)
- [ ] No "Alternative" sections for unchosen options
- [ ] No contradictory instructions (e.g., "use BLoC" and "use Riverpod")
- [ ] Project-specific context added (package name, API config, etc.)
- [ ] Code examples match your project's actual patterns
- [ ] File is self-contained — an AI agent can follow it without external docs

---

## Quick Reference: Common Stack Configurations

### Stack A: BLoC + Dio + freezed (Enterprise)

```
State Management → BLoC/Cubit (remove built-in)
Serialization   → freezed (remove json_serializable)
Networking      → Dio + Retrofit (remove http)
DI              → get_it (remove manual injection)
Lint Rules      → very_good_analysis
Architecture    → Clean Architecture with feature-based organization
```

**Example:** [examples/omedic_rules.md](./examples/omedic_rules.md)

### Stack B: Riverpod + http + json_serializable (Lightweight)

```
State Management → Riverpod
Serialization   → json_serializable
Networking      → http package
DI              → Riverpod (built-in)
Lint Rules      → flutter_lints
Architecture    → Feature-first with providers
```

### Stack C: Built-in + GoRouter (Minimal)

```
State Management → ValueNotifier / ChangeNotifier
Serialization   → json_serializable or manual
Networking      → http package
DI              → Manual constructor injection
Lint Rules      → flutter_lints
Architecture    → MVVM with manual DI
```

### Stack D: Provider + GoRouter + json_serializable (Standard)

```
State Management → Provider / ChangeNotifierProvider
Serialization   → json_serializable
Networking      → http or Dio
DI              → Provider (built-in)
Lint Rules      → flutter_lints
Architecture    → MVVM or feature-first
```
