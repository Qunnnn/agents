---
tags: [flutter, bloc, state-management, cubit]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Flutter BLoC / Cubit Pattern

## Context

Apply when creating or modifying state management using the BLoC/Cubit pattern in Flutter projects.

## Instructions

1. **Cubit vs BLoC**: Use `Cubit` for simple state, `Bloc` for event-driven flows.
2. **State classes**: Use `freezed` with a single state class and an `ApiStatus` enum (`initial`, `loading`, `success`, `error`). Freezed auto-generates `copyWith` and equality.
3. **Naming convention**:
   - Cubit: `FeatureNameCubit` → `FeatureNameState`
   - Bloc: `FeatureNameBloc` → `FeatureNameEvent` / `FeatureNameState`
4. **File structure**:
   ```
   feature_name/
   ├── cubit/
   │   ├── feature_name_cubit.dart
   │   └── feature_name_state.dart
   ├── view/
   │   └── feature_name_page.dart
   └── widgets/
       └── ...
   ```
5. **Never** put business logic in widgets — always delegate to cubit/bloc.
6. **Use** `BlocProvider` at the route level, `BlocBuilder`/`BlocSelector` in widgets.
7. **Error handling**: Emit error states with meaningful messages, catch exceptions in cubit methods.

## Examples

### ApiStatus Enum

```dart
enum ApiStatus { initial, loading, success, error }
```

### State Class

```dart
@freezed
class UserProfileState with _$UserProfileState {
  const factory UserProfileState({
    @Default(ApiStatus.initial) ApiStatus status,
    User? user,
    String? errorMessage,
  }) = _UserProfileState;
}
```

### Cubit

```dart
class UserProfileCubit extends Cubit<UserProfileState> {
  UserProfileCubit(this._repo) : super(const UserProfileState());

  final UserRepository _repo;

  Future<void> loadProfile(String userId) async {
    emit(state.copyWith(status: ApiStatus.loading));
    try {
      final user = await _repo.getUser(userId);
      emit(state.copyWith(status: ApiStatus.success, user: user));
    } catch (e) {
      emit(state.copyWith(
        status: ApiStatus.error,
        errorMessage: e.toString(),
      ));
    }
  }
}
```

### Widget Usage

```dart
BlocBuilder<UserProfileCubit, UserProfileState>(
  builder: (context, state) {
    return switch (state.status) {
      ApiStatus.initial || ApiStatus.loading => const LoadingIndicator(),
      ApiStatus.success => UserProfileView(user: state.user!),
      ApiStatus.error => ErrorView(message: state.errorMessage),
    };
  },
)
```

## Anti-patterns

- ❌ Emitting states from widgets directly
- ❌ Using `BlocBuilder` when only one field is needed (use `BlocSelector`)
- ❌ Giant monolithic blocs — split by feature
- ❌ Putting navigation logic inside blocs (use `BlocListener`)

## Resources

- https://bloclibrary.dev/getting-started
- https://bloclibrary.dev/bloc-concepts
- https://bloclibrary.dev/architecture
- https://pub.dev/packages/flutter_bloc
- https://pub.dev/packages/freezed
- https://docs.flutter.dev/data-and-backend/state-mgmt/options
