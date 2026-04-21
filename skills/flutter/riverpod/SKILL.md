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

## Provider Lifecycle

### 1. Global Singleton (`keepAlive: true`)
Use `keepAlive: true` for providers that should never be disposed, such as API clients, repositories, or local databases.
```dart
@Riverpod(keepAlive: true)
class UserRepository extends _$UserRepository {
  @override
  FutureOr<void> build() {
    // Initialization logic
  }
}
```

### 2. autoDispose (Default)
By default, providers are `autoDispose`. This is preferred for UI states (lists, details, search results) to free up memory when no longer used.
```dart
@riverpod
class TaskList extends _$TaskList {
  @override
  FutureOr<List<Task>> build() async {
    final repo = ref.watch(userRepositoryProvider.notifier);
    return repo.fetchTasks();
  }
}
```


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
Avoid watching an entire list in every list item widget, as this causes all items to rebuild when the list changes. Instead, use a family provider to watch a specific item by its ID.

```dart
@riverpod
Task individualTask(IndividualTaskRef ref, String taskId) {
  final allTasks = ref.watch(taskListProvider).value ?? [];
  return allTasks.firstWhere((t) => t.id == taskId);
}

// In Widget Item:
// final task = ref.watch(individualTaskProvider(id));
```


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

### Using AsyncValue.guard
Use `AsyncValue.guard` to wrap asynchronous operations. It automatically catches errors and converts them into `AsyncError`.

```dart
Future<void> addTask(String title) async {
  state = const AsyncLoading();
  state = await AsyncValue.guard(() async {
    final newTask = await repo.createNewTask(title);
    return [...state.value!, newTask];
  });
}
```

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
