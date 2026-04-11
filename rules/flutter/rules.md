---
tags: [flutter, dart, rules, conventions, comprehensive]
applies-to: [antigravity, cursor, copilot, gemini]
level: project
adaptable: true
---

# Flutter & Dart Project Rules

> Comprehensive rules for AI agents working on Flutter/Dart projects.
> Sections marked with `<!-- PROJECT_CONFIG -->` contain defaults that should be
> adapted per project.

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
- **Linting:** Run `dart analyze` or `flutter analyze` to catch issues.
- **Dependencies:** When suggesting new packages, explain their benefits
  and check for version compatibility with the project.
- **Respect Existing Codebase:** Before introducing new patterns, themes,
  components, or architectural decisions, **first check what the project
  already defines.** If the project has existing solutions, follow them.
  When suggesting a better alternative, explain the trade-offs and let the
  user decide — never silently replace established patterns.

---

## Project Structure

<!-- PROJECT_CONFIG: Adapt this layout to match your project -->

**Standard Structure:** Assumes a standard Flutter project with `lib/main.dart`
as the primary entry point.

### Recommended Layout

```
lib/
├── main.dart                    # Entry point
├── app.dart                     # MaterialApp / Router setup
├── core/                        # Shared utilities, constants, extensions
│   ├── constants/
│   ├── extensions/
│   ├── theme/
│   ├── utils/
│   └── widgets/                 # Shared/reusable widgets
├── features/                    # Feature-based modules
│   └── <feature_name>/
│       ├── data/                # Data sources, models, repositories impl
│       │   ├── models/
│       │   ├── datasources/
│       │   └── repositories/
│       ├── domain/              # Business logic, entities, repository contracts
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── usecases/
│       └── presentation/        # UI layer
│           ├── bloc/            # or cubit/
│           ├── pages/
│           └── widgets/
├── l10n/                        # Localization
└── injection/                   # Dependency injection setup
```

### Feature-Based Organization

For larger projects, organize code by feature. Each feature has its own
presentation, domain, and data subfolders. This improves navigability and
scalability.

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
- Use barrel files (`index.dart`) for feature exports when appropriate.

### Logging

<!-- PROJECT_CONFIG: Choose your logging approach -->

Use the `logging` package or `dart:developer` for structured logging instead of
`print`.

```dart
import 'dart:developer' as developer;

// For simple messages
developer.log('User logged in successfully.');

// For structured error logging
try {
  // ... code that might fail
} catch (e, s) {
  developer.log(
    'Failed to fetch data',
    name: 'myapp.network',
    level: 1000, // SEVERE
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
- **Switch Statements:** Prefer exhaustive `switch` statements or expressions
  (no `break` needed).
- **Exception Handling:** Use `try-catch` with specific exception types. Use
  custom exceptions for domain-specific situations.
- **Class Organization:** Define related classes within the same library file.
  For large libraries, export smaller, private libraries from a single
  top-level library.

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

Include the lint package in `analysis_options.yaml`:

<!-- PROJECT_CONFIG: Choose your lint package -->

```yaml
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    # Add project-specific lint rules here
```

---

## Application Architecture

<!-- PROJECT_CONFIG: Choose your architecture pattern -->

### Separation of Concerns

Aim for a clear separation with defined Model, View, and ViewModel/Controller
roles. Organize the project into logical layers:

| Layer | Responsibility | Contains |
|-------|---------------|----------|
| **Presentation** | UI rendering, user interaction | Widgets, screens, state management |
| **Domain** | Business logic, use cases | Entities, repository contracts, use cases |
| **Data** | Data access, transformation | Models, API clients, repository implementations |
| **Core** | Shared utilities | Constants, extensions, theme, common widgets |

---

## State Management

<!-- PROJECT_CONFIG: Choose your state management solution -->
<!-- Options: built-in (ValueNotifier/ChangeNotifier), BLoC/Cubit, Riverpod, etc. -->

### Default: Built-in Solutions

Prefer Flutter's built-in state management unless the project specifies
otherwise.

- **`ValueNotifier`** with `ValueListenableBuilder` for simple, local,
  single-value state.
- **`ChangeNotifier`** with `ListenableBuilder` for complex or shared state.
- **`StreamBuilder`** for handling sequences of asynchronous events.
- **`FutureBuilder`** for handling a single async operation.

```dart
// ValueNotifier example
final ValueNotifier<int> _counter = ValueNotifier<int>(0);

ValueListenableBuilder<int>(
  valueListenable: _counter,
  builder: (context, value, child) {
    return Text('Count: $value');
  },
);
```

### Alternative: BLoC/Cubit Pattern

When the project uses BLoC/Cubit:

- One cubit per feature screen; additional cubits for complex sub-features.
- Never access cubit state directly from another cubit — use the repository
  layer.
- Use `ApiStatus` enum in state definitions (`initial`, `loading`, `success`,
  `failure`).
- Wrap repository calls in `try-catch` within cubits.
- Emit error states with user-friendly, localized messages.

### Dependency Injection

<!-- PROJECT_CONFIG: Choose your DI approach -->

**Default:** Manual constructor injection to make dependencies explicit.

**Alternative:** Use `get_it` or `provider` for service location / DI when the
project requires it.

---

## Routing

<!-- PROJECT_CONFIG: Choose your routing solution -->

### Default: GoRouter

Use `go_router` for declarative navigation, deep linking, and web support.

```dart
final GoRouter _router = GoRouter(
  routes: <RouteBase>[
    GoRoute(
      path: '/',
      builder: (context, state) => const HomeScreen(),
      routes: <RouteBase>[
        GoRoute(
          path: 'details/:id',
          builder: (context, state) {
            final String id = state.pathParameters['id']!;
            return DetailScreen(id: id);
          },
        ),
      ],
    ),
  ],
);

// Use in MaterialApp
MaterialApp.router(routerConfig: _router);
```

- **Authentication:** Configure `go_router`'s `redirect` property for auth
  flows.
- **Navigator:** Use built-in `Navigator` only for short-lived screens like
  dialogs.

### Alternative: auto_route

When the project uses `auto_route`, follow its annotation-based routing with
code generation.

---

## Data Handling & Serialization

<!-- PROJECT_CONFIG: Choose your serialization approach -->

### Default: json_serializable

Use `json_serializable` and `json_annotation` for JSON parsing/encoding.

```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable(fieldRename: FieldRename.snake)
class User {
  final String firstName;
  final String lastName;

  User({required this.firstName, required this.lastName});

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```

### Alternative: freezed

When the project uses `freezed` for immutable models with `copyWith`, unions,
and JSON serialization:

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user.freezed.dart';
part 'user.g.dart';

@freezed
class User with _$User {
  const factory User({
    required String firstName,
    required String lastName,
  }) = _User;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}
```

---

## Networking

<!-- PROJECT_CONFIG: Choose your networking approach -->

### Default: http package

Use the standard `http` package for simple networking needs.

### Alternative: Dio + Retrofit

When the project uses Dio and Retrofit:

- Configure `Dio` with base URL, interceptors (auth, logging, error handling).
- Define `Retrofit` API interfaces with annotations.
- Run code generation after modifying API definitions.
- Abstract data sources using repository contracts for testability.

---

## Code Generation

If the project uses code generation, ensure `build_runner` is listed as a dev
dependency.

```shell
# Run code generation
dart run build_runner build --delete-conflicting-outputs

# Watch mode for development
dart run build_runner watch --delete-conflicting-outputs
```

---

## Package Management

- **Adding:** `flutter pub add <package_name>`
- **Adding dev:** `flutter pub add dev:<package_name>`
- **Removing:** `dart pub remove <package_name>`
- **Overrides:** `flutter pub add override:<package_name>:<version>`
- **Resolving:** `flutter pub get`

When suggesting new dependencies:
- Explain their benefits.
- Check for version compatibility.
- Prefer well-maintained, popular packages.
- Don't add dependencies without discussing alternatives.

---

## Testing

### Running Tests

Use `flutter test` to run all tests.

### Test Types

| Type | Package | Use For |
|------|---------|---------|
| Unit | `package:test` | Domain logic, data layer, state management |
| Widget | `package:flutter_test` | UI components |
| Integration | `package:integration_test` | End-to-end user flows |

### Conventions

- Follow the **Arrange-Act-Assert** (or Given-When-Then) pattern.
- Name test files: `<feature_name>_test.dart`.
- Use `group()` to organize related tests.
- Aim for high test coverage.

### Assertions

<!-- PROJECT_CONFIG: Choose assertion style -->

Prefer `package:checks` for more expressive assertions over default `matchers`.

### Mocking

<!-- PROJECT_CONFIG: Choose mocking approach -->

- Prefer fakes or stubs over mocks.
- When mocks are necessary, use `mocktail` or `mockito`.
- Avoid code generation for mocks when possible.

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
4. **Suggest, don't replace.** If you believe a better approach exists (e.g.,
   migrating to `ColorScheme.fromSeed`, adding `ThemeExtension`), explain
   the benefits and trade-offs, then let the user decide.

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
  home: const MyHomePage(),
);
```

### Color Scheme

- Generate harmonious palettes from a single seed color using
  `ColorScheme.fromSeed`.
- Follow the **60-30-10 rule:** 60% primary, 30% secondary, 10% accent.
- Include a wide range of color concentrations for vibrant look and feel.

### ThemeExtension for Custom Tokens

Use `ThemeExtension` for custom styles not in standard `ThemeData`:

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

// Register in ThemeData
ThemeData(
  extensions: const <ThemeExtension<dynamic>>[
    AppColors(success: Colors.green, danger: Colors.red),
  ],
);

// Access in widgets
Theme.of(context).extension<AppColors>()!.success
```

### WidgetStateProperty

Use `WidgetStateProperty.resolveWith` for state-dependent styling:

```dart
final ButtonStyle myButtonStyle = ButtonStyle(
  backgroundColor: WidgetStateProperty.resolveWith<Color>(
    (Set<WidgetState> states) {
      if (states.contains(WidgetState.pressed)) return Colors.green;
      return Colors.blue;
    },
  ),
);
```

### Typography

- Limit to 1–2 font families.
- Use `google_fonts` package for custom fonts.
- Use `Theme.of(context).textTheme` for text styles.
- Line height: **1.4x–1.6x** font size.
- Line length: **45–75 characters** for body text.
- Avoid all caps for long-form text.

```dart
textTheme: const TextTheme(
  displayLarge: TextStyle(fontSize: 57.0, fontWeight: FontWeight.bold),
  titleLarge: TextStyle(fontSize: 22.0, fontWeight: FontWeight.bold),
  bodyLarge: TextStyle(fontSize: 16.0, height: 1.5),
  bodyMedium: TextStyle(fontSize: 14.0, height: 1.4),
  labelSmall: TextStyle(fontSize: 11.0, color: Colors.grey),
),
```

---

## Layout Best Practices

### Rows and Columns

- **`Expanded`:** Fill remaining space along the main axis.
- **`Flexible`:** Shrink to fit, but not necessarily grow. Don't combine
  `Flexible` and `Expanded` in the same `Row`/`Column`.
- **`Wrap`:** When widgets would overflow, move to next line.

### General Content

- **`SingleChildScrollView`:** Content larger than viewport but fixed size.
- **`ListView.builder` / `GridView.builder`:** Always use builder constructors
  for long lists.
- **`FittedBox`:** Scale or fit a single child within its parent.
- **`LayoutBuilder`:** Complex responsive layouts based on available space.

### Stacking

- **`Positioned`:** Precisely place children in a `Stack` by anchoring to edges.
- **`Align`:** Position using `Alignment` constants.
- **`OverlayPortal`:** Show UI elements (dropdowns, tooltips) on top of
  everything.

### Responsiveness

- Use `LayoutBuilder` or `MediaQuery` for responsive UIs.
- Configure `textCapitalization`, `keyboardType`, and hints for text fields.

---

## Assets and Images

### Asset Declaration

Declare all asset paths in `pubspec.yaml`:

```yaml
flutter:
  uses-material-design: true
  assets:
    - assets/images/
    - assets/icons/
```

### Local Images

Use `Image.asset` for images from the asset bundle:

```dart
Image.asset('assets/images/logo.png')
```

### Network Images

Use `Image.network` with `loadingBuilder` and `errorBuilder` for uncached
network images:

```dart
Image.network(
  'https://example.com/image.png',
  loadingBuilder: (context, child, progress) {
    if (progress == null) return child;
    return const Center(child: CircularProgressIndicator());
  },
  errorBuilder: (context, error, stackTrace) {
    return const Icon(Icons.error);
  },
)
```

### Cached Network Images

<!-- PROJECT_CONFIG: Choose your caching package -->

**Recommended:** Use `cached_network_image_ce` (Community Edition) for
persistent disk caching. It is the actively maintained fork of the original
`cached_network_image`, which has been unmaintained since Aug 2024.

**Why CE over original:**
- Uses `hive_ce` instead of `sqflite` — up to **8x faster** cache lookups.
- Zero-jank scrolling (no Platform Channel overhead).
- True web support with persistent caching via IndexedDB.
- 99% API compatible — drop-in replacement.

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

// As ImageProvider (e.g., for DecorationImage)
Image(image: CachedNetworkImageProvider('https://example.com/image.jpg'))
```

**SVG support:** Use `unsupportedImageBuilder` with `flutter_svg`:

```dart
CachedNetworkImage(
  imageUrl: 'https://example.com/logo.svg',
  unsupportedImageBuilder: (context, url, bytes) {
    return SvgPicture.memory(bytes); // from flutter_svg
  },
  errorWidget: (context, url, error) => const Icon(Icons.error),
)
```

> **Migration note:** Switching from `cached_network_image` to
> `cached_network_image_ce` causes a one-time cache clear (SQLite → Hive).
> Users will re-download images once.

**Alternative (legacy):** If the project already uses `cached_network_image`
and migration isn't feasible, continue with the original package but be aware
of its maintenance status and potential memory leak issues.

### Custom Icons

- Use `ImageIcon` to display icons from an `ImageProvider` (custom icons not
  in the `Icons` class).



## Documentation

### API Documentation

- Write `///` doc comments for all public APIs (classes, constructors, methods,
  top-level functions).
- Start with a single-sentence summary ending with a period.
- Add a blank line after the summary for separation.

### Philosophy

- **Comment wisely:** Explain *why*, not *what*. Code should be
  self-explanatory.
- **No useless docs:** If it only restates the obvious from the name, skip it.
- **Consistency:** Use consistent terminology throughout.
- **No trailing comments.**

### Style

- Use `///` for doc comments (not `/* */`).
- Use backticks for inline code references.
- Avoid excessive markdown; never use HTML.
- Include code samples for complex APIs.
- Place doc comments before annotations.

---

## Accessibility

- **Color Contrast:** Minimum **4.5:1** for normal text, **3:1** for large text
  (WCAG 2.1).
- **Dynamic Text Scaling:** Test UI with increased system font sizes.
- **Semantic Labels:** Use the `Semantics` widget for descriptive labels.
- **Screen Reader Testing:** Test with TalkBack (Android) and VoiceOver (iOS).

---

## API Design Principles

When building reusable APIs or libraries:

- **User-Centric:** Design from the perspective of the consumer. APIs should
  be intuitive and easy to use correctly.
- **Documentation:** Good docs are part of good API design — clear, concise,
  with examples.

---

## Error Handling

<!-- PROJECT_CONFIG: Adapt to your error handling strategy -->

- Wrap repository calls in `try-catch` within state management layer.
- Emit error states with user-friendly, localized messages.
- Log raw errors for debugging; show localized errors to users.
- Define custom exception types for domain-specific errors.
- Never swallow exceptions silently.

---

> **📖 See [ADAPTATION.md](./ADAPTATION.md) for how to customize this file for
> your project.** For a real-world example, see
> [examples/omedic_rules.md](./examples/omedic_rules.md).
