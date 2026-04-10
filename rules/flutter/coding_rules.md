---
tags: [flutter, coding-style, conventions]
applies-to: [antigravity, cursor, copilot]
---

# Flutter Coding Rules

## General

- Use `const` constructors wherever possible.
- Prefer `final` over `var` for local variables that aren't reassigned.
- Maximum line length: 80 characters.
- Use trailing commas for better formatting.
- Use `part` only for generated code (`.g.dart`, `.freezed.dart`).

## Naming

- Classes: `PascalCase`
- Files: `snake_case`
- Variables/functions: `camelCase`
- Constants: `camelCase` (not `SCREAMING_SNAKE_CASE`)
- Private members: prefix with `_`
- Extensions: `XTypeName` (e.g., `XBuildContext`, `XString`)

## Widgets

- Stateless by default. Use StatefulWidget only when managing local state (animations, TextEditingController, etc.).
- Extract widgets into separate files when exceeding ~150 lines.
- Use `Key` parameter in widget constructors.
- Always name constructor parameters explicitly (named, not positional).

## State Management

- Use BLoC/Cubit pattern.
- One cubit per feature screen, additional cubits for complex sub-features.
- Never access cubit state directly from another cubit — use repository layer.

## Imports

- Use relative imports within the same package.
- Group imports: `dart:`, `package:`, relative — separated by blank lines.
- Use barrel files for feature exports.

## Testing

- Name test files: `feature_name_test.dart`
- Use `group()` to organize related tests.
- Mock dependencies using `mocktail` or `mockito`.

## Error Handling

- Wrap repository calls in try-catch within cubits.
- Emit error states with user-friendly, localized messages.
- Log raw errors for debugging; show localized errors to users.
