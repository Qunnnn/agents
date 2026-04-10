---
name: flutter-handling-http-and-json
description: Executes HTTP requests and handles JSON serialization in a Flutter app. Use when integrating with REST APIs or parsing structured data.
tags: [flutter, http, json, api, serialization, networking]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Flutter HTTP & JSON

## Contents
- [Core Guidelines](#core-guidelines)
- [Workflow: Executing HTTP Operations](#workflow-executing-http-operations)
- [Workflow: Implementing JSON Serialization](#workflow-implementing-json-serialization)
- [Workflow: Parsing Large JSON in the Background](#workflow-parsing-large-json-in-the-background)
- [Examples](#examples)

## Core Guidelines
- **Enforce HTTPS:** iOS and Android disable cleartext (HTTP) connections by default. Always use HTTPS endpoints.
- **Construct URIs Safely:** Always use `Uri.https(authority, unencodedPath, [queryParameters])` to safely build URLs.
- **Handle Status Codes:** Always validate `http.Response.statusCode`. Treat `200` (OK) and `201` (Created) as success. Throw explicit exceptions for other codes.
- **Prevent UI Jank:** Move expensive JSON parsing operations (taking >16ms) to a background isolate using `compute()`.
- **Structured AI Output:** When integrating LLMs, enforce reliable JSON output by specifying a strict JSON schema and setting `application/json` MIME type.

## Workflow: Executing HTTP Operations
Use this workflow to implement network requests using the `http` package.

- [ ] Add the `http` package to `pubspec.yaml`.
- [ ] Configure platform permissions (Internet permission in `AndroidManifest.xml` and macOS `.entitlements`).
- [ ] Construct the target `Uri`.
- [ ] Execute the HTTP method.
- [ ] Validate the response and parse the JSON payload.

**Conditional Implementation:**
- **If fetching data (GET):** Use `http.get(uri)`.
- **If sending new data (POST):** Use `http.post(uri, headers: {...}, body: jsonEncode(data))`. Ensure `Content-Type` is `application/json; charset=UTF-8`.
- **If updating data (PUT):** Use `http.put(uri, headers: {...}, body: jsonEncode(data))`.
- **If deleting data (DELETE):** Use `http.delete(uri, headers: {...})`.

## Workflow: Implementing JSON Serialization
Choose the serialization strategy based on project complexity.

- **If building a small prototype or simple models:** Use manual serialization with `dart:convert`.
- **If building a production app with complex/nested models:** Use code generation with `json_serializable`.

### Manual Serialization Setup
- [ ] Import `dart:convert`.
- [ ] Define the Model class with `final` properties.
- [ ] Implement a `factory Model.fromJson(Map<String, dynamic> json)` constructor.
- [ ] Implement a `Map<String, dynamic> toJson()` method.

### Code Generation Setup (`json_serializable`)
- [ ] Add dependencies: `flutter pub add json_annotation` and `flutter pub add -d build_runner json_serializable`.
- [ ] Import `json_annotation.dart` in the model file.
- [ ] Add the `part 'model_name.g.dart';` directive.
- [ ] Annotate the class with `@JsonSerializable()`. Use `explicitToJson: true` for nested models.
- [ ] Define the `fromJson` factory and `toJson` method delegating to the generated functions.
- [ ] Run the generator: `dart run build_runner build --delete-conflicting-outputs`.

## Workflow: Parsing Large JSON in the Background
Use this workflow to prevent frame drops when parsing large JSON payloads (1000+ items).

- [ ] Create a top-level or static function that takes a `String` and returns the parsed Dart object.
- [ ] Inside the function, call `jsonDecode` and map the results to the Model class.
- [ ] In the HTTP fetch method, pass the parsing function and `response.body` to `compute()`.

## Examples

### HTTP GET with Manual Serialization

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class Album {
  final int id;
  final String title;

  const Album({required this.id, required this.title});

  factory Album.fromJson(Map<String, dynamic> json) {
    return Album(
      id: json['id'] as int,
      title: json['title'] as String,
    );
  }
}

Future<Album> fetchAlbum() async {
  final uri = Uri.https('jsonplaceholder.typicode.com', '/albums/1');
  final response = await http.get(uri);

  if (response.statusCode == 200) {
    return Album.fromJson(jsonDecode(response.body) as Map<String, dynamic>);
  } else {
    throw Exception('Failed to load album');
  }
}
```

### HTTP POST Request

```dart
Future<Album> createAlbum(String title) async {
  final uri = Uri.https('jsonplaceholder.typicode.com', '/albums');
  final response = await http.post(
    uri,
    headers: <String, String>{
      'Content-Type': 'application/json; charset=UTF-8',
    },
    body: jsonEncode(<String, String>{'title': title}),
  );

  if (response.statusCode == 201) {
    return Album.fromJson(jsonDecode(response.body) as Map<String, dynamic>);
  } else {
    throw Exception('Failed to create album.');
  }
}
```

### Background Parsing with `compute`

```dart
import 'dart:convert';
import 'package:flutter/foundation.dart';
import 'package:http/http.dart' as http;

List<Photo> parsePhotos(String responseBody) {
  final parsed = (jsonDecode(responseBody) as List<Object?>)
      .cast<Map<String, Object?>>();
  return parsed.map<Photo>(Photo.fromJson).toList();
}

Future<List<Photo>> fetchPhotos(http.Client client) async {
  final uri = Uri.https('jsonplaceholder.typicode.com', '/photos');
  final response = await client.get(uri);

  if (response.statusCode == 200) {
    return compute(parsePhotos, response.body);
  } else {
    throw Exception('Failed to load photos');
  }
}
```

### Code Generation (`json_serializable`)

```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable(explicitToJson: true)
class User {
  final String name;

  @JsonKey(name: 'registration_date_millis')
  final int registrationDateMillis;

  User(this.name, this.registrationDateMillis);

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```
