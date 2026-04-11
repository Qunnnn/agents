---
name: flutter-handling-http-and-json
description: Executes HTTP requests and handles JSON serialization in a Flutter app using Dio and Retrofit. Use when integrating with REST APIs or parsing structured data.
tags: [flutter, dio, retrofit, http, json, api, serialization, networking, interceptors]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Flutter HTTP & JSON (Dio + Retrofit)

## Contents
- [Core Guidelines](#core-guidelines)
- [Technology Stack](#technology-stack)
- [Workflow: Setting Up Dio](#workflow-setting-up-dio)
- [Workflow: Defining API with Retrofit](#workflow-defining-api-with-retrofit)
- [Workflow: Implementing Interceptors](#workflow-implementing-interceptors)
- [Workflow: Implementing JSON Serialization](#workflow-implementing-json-serialization)
- [Workflow: Error Handling](#workflow-error-handling)
- [Examples](#examples)

## Core Guidelines
- **Use Dio** as the HTTP client — never use `dart:io` `HttpClient` or `package:http` directly. Dio provides interceptors, cancellation, timeouts, and FormData out of the box.
- **Use Retrofit** for type-safe API definitions — annotate abstract classes with `@RestApi` to auto-generate Dio client implementations.
- **Enforce HTTPS:** iOS and Android disable cleartext (HTTP) connections by default. Always use HTTPS endpoints.
- **Singleton Dio:** Create a single `Dio` instance per app (or per base URL) to share interceptors, base options, and headers consistently.
- **Always use `handler.next()`** in interceptors — never swallow requests/responses/errors without forwarding them through the chain.
- **Structured Error Handling:** Catch `DioException` and map to domain-specific exceptions. Never expose raw error messages to users.

## Technology Stack

| Package | Purpose | Type |
|---|---|---|
| `dio` | HTTP client with interceptors, timeouts, cancellation | Runtime |
| `retrofit` | Type-safe API annotations (`@RestApi`, `@GET`, `@POST`, etc.) | Runtime |
| `retrofit_generator` | Code generation for Retrofit | Dev |
| `json_annotation` | JSON serialization annotations | Runtime |
| `json_serializable` | JSON serialization code generation | Dev |
| `build_runner` | Runs code generators | Dev |

## Workflow: Setting Up Dio

- [ ] Add `dio` to `pubspec.yaml`.
- [ ] Create a singleton Dio instance with `BaseOptions` (base URL, timeouts, default headers).
- [ ] Configure platform permissions (Internet permission in `AndroidManifest.xml` and macOS `.entitlements`).
- [ ] Register interceptors: auth, logging, error handling.
- [ ] Provide the Dio instance via DI (dependency injection).

### Dio Instance Setup

```dart
Dio createDio({required String baseUrl, required String accessToken}) {
  final dio = Dio(
    BaseOptions(
      baseUrl: baseUrl,
      connectTimeout: const Duration(seconds: 30),
      receiveTimeout: const Duration(seconds: 30),
      sendTimeout: const Duration(seconds: 30),
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
    ),
  );

  dio.interceptors.addAll([
    AuthInterceptor(accessToken),
    LogInterceptor(requestBody: true, responseBody: true),
  ]);

  return dio;
}
```

## Workflow: Defining API with Retrofit

- [ ] Add dependencies: `retrofit`, `json_annotation` (runtime) and `retrofit_generator`, `json_serializable`, `build_runner` (dev).
- [ ] Create an abstract class annotated with `@RestApi(baseUrl: '...')`.
- [ ] Add a factory constructor: `factory ApiClient(Dio dio, {String? baseUrl}) = _ApiClient;`.
- [ ] Define methods using HTTP annotations: `@GET`, `@POST`, `@PUT`, `@PATCH`, `@DELETE`.
- [ ] Add the `part 'filename.g.dart';` directive.
- [ ] Run: `dart run build_runner build --delete-conflicting-outputs`.

### Retrofit Annotations Reference

| Annotation | Purpose |
|---|---|
| `@RestApi(baseUrl: '...')` | Marks an abstract class as a Retrofit API client |
| `@GET('/path')` | HTTP GET request |
| `@POST('/path')` | HTTP POST request |
| `@PUT('/path')` | HTTP PUT request |
| `@PATCH('/path')` | HTTP PATCH request |
| `@DELETE('/path')` | HTTP DELETE request |
| `@Path('name')` | URL path parameter |
| `@Query('name')` | URL query parameter |
| `@Queries()` | Map of query parameters |
| `@Body()` | Request body |
| `@Header('name')` | Request header parameter |
| `@Headers({...})` | Static request headers |
| `@Part()` | Multipart form field |
| `@MultiPart()` | Marks method as multipart |
| `@FormUrlEncoded()` | URL-encoded form data |

## Workflow: Implementing Interceptors

Interceptors run in FIFO order. Always call `handler.next()` to forward to the next interceptor.

- [ ] **Auth Interceptor:** Attach `Authorization` header to every request. Handle 401 by refreshing token and retrying.
- [ ] **Logging Interceptor:** Log requests/responses in debug mode only.
- [ ] **Error Interceptor:** Transform `DioException` into domain exceptions.

### Interceptor Structure

```dart
class AuthInterceptor extends Interceptor {
  final String _token;
  AuthInterceptor(this._token);

  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    options.headers['Authorization'] = 'Bearer $_token';
    handler.next(options); // ← MUST call handler.next()
  }

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    if (err.response?.statusCode == 401) {
      // Handle token refresh logic here
    }
    handler.next(err); // ← MUST call handler.next()
  }
}
```

### QueuedInterceptor

Use `QueuedInterceptor` instead of `Interceptor` when interceptor logic is async (e.g., token refresh). This ensures requests are queued and processed one at a time, preventing race conditions during token refresh.

```dart
class TokenRefreshInterceptor extends QueuedInterceptor {
  @override
  void onError(DioException err, ErrorInterceptorHandler handler) async {
    if (err.response?.statusCode == 401) {
      // Refresh token — all other requests will queue behind this
      final newToken = await refreshToken();
      err.requestOptions.headers['Authorization'] = 'Bearer $newToken';
      // Retry the failed request
      final response = await dio.fetch(err.requestOptions);
      handler.resolve(response);
      return;
    }
    handler.next(err);
  }
}
```

## Workflow: Implementing JSON Serialization

Use code generation with `json_serializable` for all production models.

- [ ] Add dependencies: `json_annotation` (runtime) and `json_serializable`, `build_runner` (dev).
- [ ] Import `json_annotation.dart` in the model file.
- [ ] Add the `part 'model_name.g.dart';` directive.
- [ ] Annotate the class with `@JsonSerializable()`. Use `explicitToJson: true` for nested models.
- [ ] Define the `fromJson` factory and `toJson` method delegating to generated functions.
- [ ] Run: `dart run build_runner build --delete-conflicting-outputs`.

> **Important:** Retrofit requires a `factory Model.fromJson(Map<String, dynamic> json)` on every response model for automatic type conversion.

## Workflow: Error Handling

- [ ] Wrap all API calls in try-catch blocks catching `DioException`.
- [ ] Map `DioExceptionType` to user-friendly error messages.
- [ ] Use `ErrorInterceptor` for global error transformation.

### DioExceptionType Reference

| Type | Meaning |
|---|---|
| `connectionTimeout` | Server unreachable within timeout |
| `sendTimeout` | Request sending exceeded timeout |
| `receiveTimeout` | Response receiving exceeded timeout |
| `badResponse` | Server returned a non-success status code |
| `cancel` | Request was manually cancelled |
| `connectionError` | Network connection error |
| `badCertificate` | SSL certificate issue |
| `unknown` | Unexpected error |

## Examples

### Full API Client with Retrofit

```dart
import 'package:dio/dio.dart';
import 'package:json_annotation/json_annotation.dart';
import 'package:retrofit/retrofit.dart';

part 'user_api.g.dart';

@RestApi(baseUrl: 'https://api.example.com/v1')
abstract class UserApi {
  factory UserApi(Dio dio, {String? baseUrl}) = _UserApi;

  @GET('/users')
  Future<List<User>> getUsers();

  @GET('/users/{id}')
  Future<User> getUser(@Path('id') String id);

  @POST('/users')
  Future<User> createUser(@Body() CreateUserRequest request);

  @PUT('/users/{id}')
  Future<User> updateUser(
    @Path('id') String id,
    @Body() UpdateUserRequest request,
  );

  @DELETE('/users/{id}')
  Future<void> deleteUser(@Path('id') String id);

  @GET('/users')
  Future<List<User>> searchUsers(
    @Query('q') String query,
    @Query('page') int page,
    @Query('limit') int limit,
  );
}
```

### Model with `json_serializable`

```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable(explicitToJson: true)
class User {
  final int id;
  final String name;

  @JsonKey(name: 'email_address')
  final String email;

  @JsonKey(name: 'created_at')
  final DateTime createdAt;

  const User({
    required this.id,
    required this.name,
    required this.email,
    required this.createdAt,
  });

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```

### Error Handling at Repository Level

```dart
class UserRepository {
  final UserApi _api;
  UserRepository(this._api);

  Future<List<User>> getUsers() async {
    try {
      return await _api.getUsers();
    } on DioException catch (e) {
      switch (e.type) {
        case DioExceptionType.connectionTimeout:
        case DioExceptionType.receiveTimeout:
          throw NetworkTimeoutException();
        case DioExceptionType.badResponse:
          final statusCode = e.response?.statusCode;
          if (statusCode == 404) throw NotFoundException();
          if (statusCode == 401) throw UnauthorizedException();
          throw ServerException(statusCode: statusCode);
        case DioExceptionType.connectionError:
          throw NoInternetException();
        default:
          throw UnknownApiException(e.message);
      }
    }
  }
}
```

### File Upload with Retrofit

```dart
@POST('/upload')
@MultiPart()
Future<UploadResponse> uploadFile(
  @Part(name: 'file') File file,
  @Part(name: 'description') String description,
);
```

### Getting Raw HttpResponse

```dart
// Wrap return type in HttpResponse to access status code and headers
@GET('/users/{id}')
Future<HttpResponse<User>> getUserWithResponse(@Path('id') String id);

// Usage
final httpResponse = await api.getUserWithResponse('123');
print(httpResponse.response.statusCode); // 200
print(httpResponse.data.name);           // User name
```

## Anti-patterns

- ❌ Using `package:http` or raw `HttpClient` instead of Dio
- ❌ Creating a new `Dio()` instance per request (use singleton)
- ❌ Forgetting `handler.next()` in interceptors (swallows the request/response)
- ❌ Writing manual HTTP calls when Retrofit can generate them
- ❌ Missing `fromJson` factory on response models (Retrofit can't deserialize)
- ❌ Catching `Exception` instead of `DioException` for network errors
- ❌ Exposing `DioException` to the UI layer (map to domain exceptions)

## Resources

- https://pub.dev/packages/dio
- https://pub.dev/packages/retrofit
- https://pub.dev/packages/json_serializable
- https://pub.dev/packages/json_annotation
- https://docs.flutter.dev/data-and-backend/networking
- https://docs.flutter.dev/data-and-backend/serialization/json
- https://docs.flutter.dev/cookbook/networking/fetch-data
- https://docs.flutter.dev/release/breaking-changes/network-policy-ios-android
