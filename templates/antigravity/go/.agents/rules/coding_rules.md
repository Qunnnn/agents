---
tags: [go, coding-style, conventions]
applies-to: [antigravity, cursor, copilot]
---

# Go Coding Rules

## General

- Follow standard Go formatting (`gofmt` / `goimports`).
- Keep functions short — aim for < 50 lines.
- Use meaningful variable names; short names only in tight scopes.
- Return errors, don't panic.

## Project Layout

```
cmd/           # Application entry points
internal/      # Private application code
  handler/     # HTTP handlers
  service/     # Business logic
  repository/  # Data access
  model/       # Domain models
  middleware/  # HTTP middleware
pkg/           # Public library code
migrations/    # Database migrations
```

## Naming

- Packages: lowercase, single word (e.g., `handler`, `service`)
- Interfaces: verb-based or role-based (`Reader`, `UserService`)
- Errors: `Err` prefix (`ErrNotFound`, `ErrUnauthorized`)
- Constructors: `New` prefix (`NewUserService`)

## Error Handling

- Always handle errors explicitly — no `_` for error return values.
- Wrap errors with context using `fmt.Errorf("action: %w", err)`.
- Define sentinel errors for known conditions.
- Use custom error types for complex error scenarios.

## API Design

- Use proper HTTP methods (GET, POST, PUT, DELETE).
- Return consistent JSON response structures.
- Always validate input before processing.
- Use middleware for cross-cutting concerns (auth, logging, CORS).

## Database

- Use migrations for schema changes (golang-migrate).
- Use parameterized queries (never string concatenation).
- Close resources with `defer`.
- Use transactions for multi-step operations.

## Testing

- Table-driven tests for multiple scenarios.
- Use interfaces for mockable dependencies.
- Test file naming: `*_test.go` in same package.
