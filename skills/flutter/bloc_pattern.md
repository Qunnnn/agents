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
2. **State classes**: Use `freezed` or sealed classes for states. Always include `initial`, `loading`, `loaded`, and `error` states.
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

### Cubit

```dart
class UserProfileCubit extends Cubit<UserProfileState> {
  UserProfileCubit(this._repo) : super(const UserProfileState.initial());

  final UserRepository _repo;

  Future<void> loadProfile(String userId) async {
    emit(const UserProfileState.loading());
    try {
      final user = await _repo.getUser(userId);
      emit(UserProfileState.loaded(user));
    } catch (e) {
      emit(UserProfileState.error(e.toString()));
    }
  }
}
```

### State with Freezed

```dart
@freezed
class UserProfileState with _$UserProfileState {
  const factory UserProfileState.initial() = _Initial;
  const factory UserProfileState.loading() = _Loading;
  const factory UserProfileState.loaded(User user) = _Loaded;
  const factory UserProfileState.error(String message) = _Error;
}
```

## Anti-patterns

- ❌ Emitting states from widgets directly
- ❌ Using `BlocBuilder` when only one field is needed (use `BlocSelector`)
- ❌ Giant monolithic blocs — split by feature
- ❌ Putting navigation logic inside blocs (use `BlocListener`)
