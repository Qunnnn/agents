---
tags: [flutter, dart, rules, omedic, bloc, dio, freezed]
applies-to: [antigravity, cursor, copilot, gemini]
level: project
based-on: rules/flutter/rules.md
---

# Omedic — Flutter Project Rules

> Project-specific rules adapted from the Flutter rules template.
> Stack: **Clean Architecture + BLoC/Cubit + Dio/Retrofit + freezed + get_it**

You are an expert in Flutter and Dart development. Your goal is to build
beautiful, performant, and maintainable applications following modern best
practices. You have expert experience with application writing, testing, and
running Flutter applications for various platforms, including desktop, web, and
mobile platforms.

---

## Interaction Guidelines

- **User Persona:** Assume the user is familiar with programming concepts.
  Explain Dart-specific features (null safety, futures, streams) when relevant.
- **Clarification:** If a request is ambiguous, ask for clarification on the
  intended functionality and the target platform.
- **Formatting:** Use `dart format` to ensure consistent code formatting.
- **Fixes:** Use `dart fix --apply` to automatically fix common errors and
  conform to configured analysis options.
- **Linting:** Run `flutter analyze` to catch issues.
- **Dependencies:** When suggesting new packages, explain their benefits
  and check for version compatibility with the project.
- **Respect Existing Codebase:** Before introducing new patterns, themes,
  components, or architectural decisions, **first check what the project
  already defines.** If the project has existing solutions, follow them.
  When suggesting a better alternative, explain the trade-offs and let the
  user decide — never silently replace established patterns.

---

## Project Structure

```
lib/
├── main_dev.dart                # Entry point — dev flavor
├── main_staging.dart            # Entry point — staging flavor
├── main_prod.dart               # Entry point — production flavor
├── app.dart                     # MaterialApp / Router setup
├── core/
│   ├── constants/               # App-wide constants
│   ├── extensions/              # Extension methods (XBuildContext, XString, etc.)
│   ├── l10n/                    # Localization
│   ├── network/                 # Dio client, interceptors, API config
│   │   ├── dio_client.dart
│   │   ├── auth_interceptor.dart
│   │   └── error_interceptor.dart
│   ├── theme/                   # ThemeData, colors, text styles
│   ├── utils/                   # Helper functions
│   └── widgets/                 # Shared/reusable widgets
├── features/
│   └── <feature_name>/
│       ├── data/
│       │   ├── models/          # freezed models + .g.dart / .freezed.dart
│       │   ├── datasources/     # Retrofit API interfaces
│       │   └── repositories/    # Repository implementations
│       ├── domain/
│       │   ├── entities/        # Domain entities (optional, if different from models)
│       │   ├── repositories/    # Abstract repository contracts
│       │   └── usecases/        # Business logic use cases
│       └── presentation/
│           ├── cubit/           # Cubit + state classes
│           ├── pages/           # Full-screen pages
│           └── widgets/         # Feature-specific widgets
└── injection/
    ├── injection.dart           # get_it setup
    └── injection.config.dart    # injectable generated config
```

---

## Code Quality

### General Principles

- **SOLID Principles:** Apply throughout the codebase.
- **Composition over Inheritance:** Favor composition for building complex
  widgets and logic.
- **Conciseness:** Write code that is as short as it can be while remaining
  clear.
- **Simplicity:** Write straightforward code. Clever or obscure code is
  difficult to maintain.
- **Immutability:** Prefer immutable data structures. Widgets (especially
  `StatelessWidget`) should be immutable.
- **Error Handling:** Anticipate and handle potential errors. Don't let code
  fail silently.

### Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Classes / Enums / Typedefs | `PascalCase` | `UserProfile`, `ApiStatus` |
| Files | `snake_case` | `user_profile.dart` |
| Variables / Functions | `camelCase` | `firstName`, `fetchUser()` |
| Constants | `camelCase` | `defaultPadding` (not `SCREAMING_SNAKE`) |
| Private members | `_` prefix | `_controller`, `_buildHeader()` |
| Extensions | `X` prefix | `XBuildContext`, `XString` |

### Styling

- **Line length:** 80 characters or fewer.
- **Trailing commas:** Always use for better formatting and cleaner diffs.
- **`part` directive:** Use only for generated code (`.g.dart`, `.freezed.dart`).

### Functions

- Keep functions short and single-purpose. Strive for less than 20 lines.
- Use arrow syntax for simple one-line functions.

### Imports

- Use relative imports within the same package.
- Group imports: `dart:` → `package:` → relative — separated by blank lines.
- Use barrel files for feature exports when appropriate.

### Logging

Use `dart:developer` for structured logging. Never use `print` in production
code.

```dart
import 'dart:developer' as developer;

developer.log('User logged in', name: 'omedic.auth');

try {
  // ...
} catch (e, s) {
  developer.log(
    'Failed to fetch data',
    name: 'omedic.network',
    level: 1000,
    error: e,
    stackTrace: s,
  );
}
```

---

## Dart Best Practices

- **Effective Dart:** Follow the official [Effective Dart](https://dart.dev/effective-dart)
  guidelines.
- **Null Safety:** Write soundly null-safe code. Avoid `!` unless the value is
  guaranteed to be non-null.
- **Async/Await:** Ensure proper use of `async`/`await` with robust error
  handling.
  - Use `Future`s for single async operations.
  - Use `Stream`s for sequences of asynchronous events.
- **Pattern Matching:** Use pattern matching features where they simplify code.
- **Records:** Use records to return multiple types where defining a class is
  cumbersome.
- **Switch Statements:** Prefer exhaustive `switch` statements or expressions.
- **Exception Handling:** Use `try-catch` with specific exception types. Use
  custom exceptions for domain-specific situations.

---

## Flutter Best Practices

### Widget Design

- **Immutability:** Widgets are immutable; Flutter rebuilds the widget tree
  when UI changes.
- **Composition:** Prefer composing smaller widgets over extending existing
  ones. Use this to avoid deep widget nesting.
- **Private Widgets:** Use small, private `Widget` classes instead of private
  helper methods that return a `Widget`.
- **Build Methods:** Break down large `build()` methods into smaller, reusable
  private Widget classes.
- **Stateless by default:** Use `StatefulWidget` only when managing local state
  (animations, `TextEditingController`, focus, etc.).
- **Extract large widgets:** Extract into separate files when exceeding ~150
  lines.
- **Key parameter:** Include `Key` in widget constructors.
- **Named parameters:** Always use named parameters, not positional.

### Performance

- **Const Constructors:** Use `const` constructors and in `build()` methods
  whenever possible to reduce rebuilds.
- **List Performance:** Use `ListView.builder` or `SliverList` for long lists
  (lazy-loaded).
- **Isolates:** Use `compute()` or `Isolate.run()` for expensive calculations
  (e.g., JSON parsing) to avoid blocking the UI thread.
- **Build Method Purity:** Never perform expensive operations (network calls,
  complex computations) directly within `build()` methods.
- **Prefer `final`:** Use `final` over `var` for local variables that aren't
  reassigned.

---

## Lint Rules

```yaml
include: package:very_good_analysis/analysis_options.yaml

linter:
  rules:
    public_member_api_docs: false
    # Add project-specific overrides here
```

---

## Application Architecture

### Clean Architecture with BLoC

Organize the project in three layers with strict dependency rules:

| Layer | Responsibility | Contains |
|-------|---------------|----------|
| **Presentation** | UI rendering, user interaction | Widgets, screens, Cubits |
| **Domain** | Business logic, contracts | Entities, repository interfaces, use cases |
| **Data** | Data access, transformation | Models (freezed), Retrofit APIs, repository implementations |
| **Core** | Shared utilities | Constants, extensions, theme, common widgets, Dio config |

**Dependency rule:** Presentation → Domain ← Data. Domain never depends on
Data or Presentation.

---

## State Management: BLoC/Cubit

- One Cubit per feature screen; additional Cubits for complex sub-features.
- Never access cubit state directly from another cubit — use the repository
  layer.
- Use `ApiStatus` enum in state definitions.
- Wrap repository calls in `try-catch` within cubits.
- Emit error states with user-friendly, localized messages.

### State Structure

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user_state.freezed.dart';

enum ApiStatus { initial, loading, success, failure }

@freezed
class UserState with _$UserState {
  const factory UserState({
    @Default(ApiStatus.initial) ApiStatus status,
    User? user,
    String? errorMessage,
  }) = _UserState;
}
```

### Cubit Pattern

```dart
class UserCubit extends Cubit<UserState> {
  UserCubit(this._userRepository) : super(const UserState());

  final UserRepository _userRepository;

  Future<void> fetchUser(String id) async {
    emit(state.copyWith(status: ApiStatus.loading));
    try {
      final user = await _userRepository.getUser(id);
      emit(state.copyWith(status: ApiStatus.success, user: user));
    } catch (e) {
      emit(state.copyWith(
        status: ApiStatus.failure,
        errorMessage: e.toString(),
      ));
    }
  }
}
```

### Dependency Injection: get_it + injectable

```dart
// injection.dart
import 'package:get_it/get_it.dart';
import 'package:injectable/injectable.dart';

import 'injection.config.dart';

final getIt = GetIt.instance;

@InjectableInit()
void configureDependencies() => getIt.init();
```

Register with annotations:
- `@injectable` for classes
- `@lazySingleton` for singletons
- `@module` for third-party dependencies (Dio, SharedPreferences)

---

## Routing: auto_route

Use `auto_route` with annotation-based routing and code generation.

```dart
@AutoRouterConfig()
class AppRouter extends RootStackRouter {
  @override
  List<AutoRoute> get routes => [
    AutoRoute(page: HomeRoute.page, initial: true),
    AutoRoute(page: ProfileRoute.page),
    AutoRoute(page: SettingsRoute.page),
  ];
}
```

Run code generation after modifying routes:

```shell
dart run build_runner build --delete-conflicting-outputs
```

---

## Data Models: freezed

Use `freezed` for all data models. Provides immutability, `copyWith`, `==`,
`toString`, and optional JSON serialization.

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user.freezed.dart';
part 'user.g.dart';

@freezed
class User with _$User {
  const factory User({
    required int id,
    required String firstName,
    required String lastName,
    String? email,
  }) = _User;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}
```

### Conventions

- One model per file.
- Field names use `camelCase`; JSON mapping handled by `@JsonKey` or
  `fieldRename` when needed.
- Use `@Default(value)` for default values.
- Use union types (sealed classes) for states that have distinctly different
  shapes.

---

## Networking: Dio + Retrofit

### Dio Client Setup

```dart
@module
abstract class NetworkModule {
  @lazySingleton
  Dio dio() {
    final dio = Dio(BaseOptions(
      baseUrl: Env.apiBaseUrl,
      connectTimeout: const Duration(seconds: 30),
      receiveTimeout: const Duration(seconds: 30),
      headers: {'Content-Type': 'application/json'},
    ));

    dio.interceptors.addAll([
      AuthInterceptor(getIt<TokenStorage>()),
      ErrorInterceptor(),
      LogInterceptor(requestBody: true, responseBody: true),
    ]);

    return dio;
  }
}
```

### Retrofit API Interface

```dart
import 'package:retrofit/retrofit.dart';
import 'package:dio/dio.dart';

part 'user_api.g.dart';

@RestApi()
abstract class UserApi {
  factory UserApi(Dio dio) = _UserApi;

  @GET('/users/{id}')
  Future<User> getUser(@Path('id') String id);

  @POST('/users')
  Future<User> createUser(@Body() CreateUserRequest request);

  @GET('/users')
  Future<List<User>> getUsers(@Query('page') int page);
}
```

### Repository Pattern

```dart
// Domain layer — contract
abstract class UserRepository {
  Future<User> getUser(String id);
  Future<List<User>> getUsers(int page);
}

// Data layer — implementation
@LazySingleton(as: UserRepository)
class UserRepositoryImpl implements UserRepository {
  UserRepositoryImpl(this._userApi);

  final UserApi _userApi;

  @override
  Future<User> getUser(String id) => _userApi.getUser(id);

  @override
  Future<List<User>> getUsers(int page) => _userApi.getUsers(page);
}
```

---

## Code Generation

`build_runner` is required. Run after modifying:
- freezed models
- Retrofit API interfaces
- auto_route routes
- injectable registrations

```shell
# One-time build
dart run build_runner build --delete-conflicting-outputs

# Watch mode
dart run build_runner watch --delete-conflicting-outputs
```

---

## Build Flavors & Environment

```bash
# Development
flutter run --flavor dev --dart-define-from-file=env/.env.dev

# Staging
flutter run --flavor staging --dart-define-from-file=env/.env.staging

# Production
flutter run --flavor prod --dart-define-from-file=env/.env.prod
```

Environment variables are accessed via generated `Env` class. Never hardcode
API URLs, keys, or secrets.

---

## Package Management

- **Adding:** `flutter pub add <package_name>`
- **Adding dev:** `flutter pub add dev:<package_name>`
- **Removing:** `dart pub remove <package_name>`
- **Resolving:** `flutter pub get`

When suggesting new dependencies:
- Explain their benefits.
- Check for version compatibility.
- Prefer well-maintained, popular packages.
- Don't add dependencies without discussing alternatives.

---

## Testing

### Running Tests

```bash
flutter test                     # All tests
flutter test test/features/auth  # Feature-specific tests
```

### Test Types

| Type | Package | Use For |
|------|---------|---------|
| Unit | `package:test` | Cubits, repositories, use cases |
| Widget | `package:flutter_test` | UI components |
| Integration | `package:integration_test` | End-to-end user flows |

### Conventions

- Follow the **Arrange-Act-Assert** (Given-When-Then) pattern.
- Name test files: `<feature_name>_test.dart`.
- Use `group()` to organize related tests.
- Mock dependencies using `mocktail`.
- Use `bloc_test` for testing Cubits/BLoCs.

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:mocktail/mocktail.dart';

class MockUserRepository extends Mock implements UserRepository {}

void main() {
  late UserCubit cubit;
  late MockUserRepository mockRepo;

  setUp(() {
    mockRepo = MockUserRepository();
    cubit = UserCubit(mockRepo);
  });

  blocTest<UserCubit, UserState>(
    'emits [loading, success] when fetchUser succeeds',
    build: () {
      when(() => mockRepo.getUser('1'))
          .thenAnswer((_) async => const User(id: 1, firstName: 'John', lastName: 'Doe'));
      return cubit;
    },
    act: (cubit) => cubit.fetchUser('1'),
    expect: () => [
      const UserState(status: ApiStatus.loading),
      const UserState(
        status: ApiStatus.success,
        user: User(id: 1, firstName: 'John', lastName: 'Doe'),
      ),
    ],
  );
}
```

---

## Theming

### Respect Existing Design System

**BEFORE creating or suggesting theme changes:**

1. **Discover** what the project already defines — check for existing
   `ThemeData`, `ColorScheme`, `TextTheme`, `ThemeExtension`, and custom
   widget components.
2. **Follow** the existing design system. Use the project's defined colors,
   text styles, spacing, and component patterns.
3. **Reuse** existing shared widgets from `core/widgets/` or the project's
   component library before creating new ones.
4. **Suggest, don't replace.** If you believe a better approach exists,
   explain the benefits and trade-offs, then let the user decide.

### Centralized Theme

Define a centralized `ThemeData` with support for both light and dark modes.

```dart
MaterialApp(
  theme: ThemeData(
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.deepPurple,
      brightness: Brightness.light,
    ),
    textTheme: const TextTheme(
      displayLarge: TextStyle(fontSize: 57.0, fontWeight: FontWeight.bold),
      bodyMedium: TextStyle(fontSize: 14.0, height: 1.4),
    ),
  ),
  darkTheme: ThemeData(
    colorScheme: ColorScheme.fromSeed(
      seedColor: Colors.deepPurple,
      brightness: Brightness.dark,
    ),
  ),
);
```

### ThemeExtension for Custom Tokens

```dart
@immutable
class AppColors extends ThemeExtension<AppColors> {
  const AppColors({required this.success, required this.danger});

  final Color? success;
  final Color? danger;

  @override
  ThemeExtension<AppColors> copyWith({Color? success, Color? danger}) {
    return AppColors(
      success: success ?? this.success,
      danger: danger ?? this.danger,
    );
  }

  @override
  ThemeExtension<AppColors> lerp(ThemeExtension<AppColors>? other, double t) {
    if (other is! AppColors) return this;
    return AppColors(
      success: Color.lerp(success, other.success, t),
      danger: Color.lerp(danger, other.danger, t),
    );
  }
}
```

### Typography

- Limit to 1–2 font families.
- Use `google_fonts` package for custom fonts.
- Use `Theme.of(context).textTheme` for text styles.

---

## Layout Best Practices

- **`Expanded` / `Flexible`:** Don't combine in the same `Row`/`Column`.
- **`ListView.builder` / `GridView.builder`:** Always use builder constructors.
- **`LayoutBuilder` / `MediaQuery`:** For responsive UIs.
- **`OverlayPortal`:** For dropdowns and tooltips on top of content.

---

## Assets and Images

- Declare asset paths in `pubspec.yaml`.
- Use `Image.asset` for local images.
- Use `cached_network_image_ce` (Community Edition) for cached network images.
  It replaces `sqflite` with `hive_ce` for ~8x faster cache lookups and
  zero-jank scrolling.
- Always include `errorWidget` for network images.

```dart
import 'package:cached_network_image_ce/cached_network_image.dart';

// With placeholder
CachedNetworkImage(
  imageUrl: 'https://example.com/image.jpg',
  placeholder: (context, url) => const CircularProgressIndicator(),
  errorWidget: (context, url, error) => const Icon(Icons.error),
)

// With progress indicator
CachedNetworkImage(
  imageUrl: 'https://example.com/image.jpg',
  progressIndicatorBuilder: (context, url, progress) =>
      CircularProgressIndicator(value: progress.progress),
  errorWidget: (context, url, error) => const Icon(Icons.error),
)

// As ImageProvider
Image(image: CachedNetworkImageProvider('https://example.com/image.jpg'))
```

---

## Documentation

- Write `///` doc comments for all public APIs.
- Start with a single-sentence summary ending with a period.
- Explain *why*, not *what*. Code should be self-explanatory.
- No trailing comments. No useless docs.
- Place doc comments before annotations.

---

## Accessibility

- **Color Contrast:** Minimum **4.5:1** for normal text (WCAG 2.1).
- **Dynamic Text Scaling:** Test UI with increased system font sizes.
- **Semantic Labels:** Use the `Semantics` widget for descriptive labels.
- **Screen Reader Testing:** Test with TalkBack (Android) and VoiceOver (iOS).

---

## Error Handling

- Wrap repository calls in `try-catch` within Cubits.
- Emit error states with user-friendly, localized messages.
- Log raw errors with `dart:developer`; show localized errors to users.
- Define custom exception types for domain-specific errors.
- Never swallow exceptions silently.
