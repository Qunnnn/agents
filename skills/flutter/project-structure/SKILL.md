---
tags: [flutter, architecture, clean-architecture, modular]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Flutter Project Structure

## Context

Apply when creating new features, modules, or navigating the codebase in a modular Flutter project.

## Instructions

1. **Feature-first organization**: Group by feature, not by type.
2. **Module structure**:
   ```
   lib/
   ├── app/                        # App-level config (theme, routing, DI)
   ├── core/                       # Shared utilities, constants, extensions
   │   ├── extensions/
   │   ├── constants/
   │   ├── utils/
   │   └── widgets/                # Shared/reusable widgets
   ├── data/                       # Data layer
   │   ├── models/
   │   ├── repositories/
   │   └── datasources/
   └── features/                   # Feature modules
       └── feature_name/
           ├── cubit/              # State management
           ├── view/               # Pages/screens
           ├── widgets/            # Feature-specific widgets
           └── _shared/            # Shared within feature
   ```
3. **Import rules**:
   - Features should NOT import from other features directly
   - Use the `core/` layer for shared logic
   - Data layer is accessed via repository interfaces
4. **Widget decomposition**: If a widget exceeds ~150 lines, decompose into smaller widgets.
5. **Barrel files**: Use `index.dart` or feature-named exports to keep imports clean.

## Anti-patterns

- ❌ Cross-feature imports (feature A importing from feature B directly)
- ❌ Business logic in widgets
- ❌ Monolithic files with `part` directives for non-generated code
- ❌ Deeply nested widget trees without extraction

## Resources

- https://docs.flutter.dev/app-architecture
- https://docs.flutter.dev/app-architecture/concepts
- https://docs.flutter.dev/app-architecture/guide
- https://docs.flutter.dev/app-architecture/recommendations
- https://docs.flutter.dev/resources/architectural-overview
