---
tags: [general, error-handling, best-practices]
applies-to: [antigravity, cursor, copilot]
level: snippet
---

# Error Handling Best Practices

## Context

Apply when implementing error handling in any language or framework.

## Instructions

1. **Be specific**: Catch specific exceptions, not generic ones.
2. **User-facing vs internal**: Separate user-facing error messages from internal error details.
3. **Error localization**: Always localize user-facing error messages.
4. **Logging**: Log the full error (including stack trace) server-side; show friendly messages client-side.
5. **Error types**: Define domain-specific error/exception classes.
6. **Recovery**: Always consider: can the operation be retried? Show retry option when applicable.
7. **Fail fast**: Validate inputs early, fail before expensive operations.

## Examples

### Dart/Flutter

```dart
class BlockchainException implements Exception {
  final String rawMessage;
  final String? localizedMessage;
  
  BlockchainException(this.rawMessage, {this.localizedMessage});
  
  String toUserMessage(BuildContext context) {
    return localizedMessage ?? ErrorLocalizer.localize(context, rawMessage);
  }
}
```

### Go

```go
type AppError struct {
    Code    string `json:"code"`
    Message string `json:"message"`
    Err     error  `json:"-"` // internal, never exposed
}

func (e *AppError) Error() string { return e.Message }
```

## Anti-patterns

- ❌ Swallowing errors silently (`catch (e) {}`)
- ❌ Exposing raw error messages to users
- ❌ Using strings for error identification (use error codes/types)
- ❌ Catching generic `Exception` when a specific type is expected
