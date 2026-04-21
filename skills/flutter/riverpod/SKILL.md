---
name: flutter-riverpod-pattern
description: Implements state management in Flutter applications using Riverpod 2.x and Code Generation.
tags: [flutter, riverpod, state-management, providers]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Flutter Riverpod Pattern

## Context

Apply when creating or modifying state management using Riverpod in Flutter projects. We use Riverpod 2.x with code generation (`riverpod_annotation`).

## Instructions

1. **Code Generation**: Always use `@riverpod` and `riverpod_annotation`. Avoid manually writing `Provider`, `FutureProvider`, etc.
2. **State classes**: Use `freezed` for complex states to benefit from deep equality, or rely on `AsyncValue` for asynchronous operations.
3. **Naming convention**:
   - Class: `FeatureName` extending `_$FeatureName`
   - File: `feature_name_provider.dart`
4. **File structure**:
   ```text
   feature_name/
   ├── providers/
   │   └── feature_name_provider.dart
   ├── pages/
   │   └── feature_name_page.dart
   └── widgets/
       └── ...
   ```
5. **Never** put business logic in widgets — always delegate to the Riverpod Notifier.
6. **Use** `ConsumerWidget` or `ConsumerStatefulWidget` instead of `StatelessWidget` / `StatefulWidget`.
7. **Error handling**: Rely on `AsyncValue.guard()` to catch exceptions and automatically emit `AsyncError`.

## UI Performance & Optimization

To ensure smooth UI and minimize unnecessary rebuilds, follow these optimization patterns:

### 1. Selective Rebuilds (`ref.select`)
When watching a provider that returns a complex object, use `.select` to watch only the specific field you need. The widget will only rebuild if *that specific field* changes.

```dart
// ❌ BAD: Rebuilds when ANY part of user changes
final user = ref.watch(userProvider);
return Text(user.name);

// ✅ GOOD: Rebuilds ONLY when name changes
final name = ref.watch(userProvider.select((u) => u.name));
return Text(name);
```

### 2. Granular Rebuilds (`Consumer`)
Instead of putting `ref.watch` at the top of a large `build` method, wrap only the small part of the UI that needs the state in a `Consumer` widget.

```dart
Widget build(BuildContext context, WidgetRef ref) {
  // This scaffold and its static parts won't rebuild
  return Scaffold(
    appBar: AppBar(title: const Text('Settings')),
    body: Column(
      children: [
        const StaticHeader(), // Never rebuilds
        Consumer(
          builder: (context, ref, child) {
            // Only this text rebuilds when the state changes
            final themeMode = ref.watch(settingsProvider.select((s) => s.themeMode));
            return Text('Current mode: $themeMode');
          },
        ),
      ],
    ),
  );
}
```

### 3. Side Effects without Rebuilds (`ref.listen`)
Use `ref.listen` for actions like showing snackbars, navigating, or showing dialogs. It reacts to state changes without triggering a build of the widget where it's used.

```dart
@override
Widget build(BuildContext context, WidgetRef ref) {
  ref.listen(authNotifierProvider, (previous, next) {
    if (next is AsyncError) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text(next.error.toString())),
      );
    }
  });
  
  return const LoginForm();
}
```

### 4. Optimization for Lists
When displaying a list, avoid watching the entire list in every list item. Pass the item itself or its ID/index to the item widget, or use a provider that returns just that specific item.

## Examples

### Optimized AsyncNotifier

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

part 'user_profile_provider.g.dart';

@riverpod
class UserProfile extends _$UserProfile {
  @override
  FutureOr<User> build(String userId) async {
    final repo = ref.watch(userRepositoryProvider);
    return repo.getUser(userId);
  }
  // ... methods
}
```

## Anti-patterns

- ❌ **Massive Rebuilds**: Watching a global "AppState" object at the root of a page without `.select`.
- ❌ **Logic in build**: Performing complex calculations or filtering inside `build()`. Move these to a computed provider using `ref.watch`.
- ❌ **Missing const**: Not using `const` constructors for widgets that don't depend on providers.
- ❌ **Passing ref**: Passing `WidgetRef` as a parameter to child widgets instead of using `Consumer`.
- ❌ **Manual rebuilds**: Using `setState` in a `ConsumerStatefulWidget` for data that should be in a provider.

## Resources

- https://riverpod.dev/docs/concepts/about_code_generation
- https://riverpod.dev/docs/concepts/reading#using-select-to-filter-rebuilds
- https://riverpod.dev/docs/concepts/modifiers/family
- https://pub.dev/packages/riverpod_annotation
