---
tags: [go, api, handler, rest, gin]
applies-to: [antigravity, cursor, copilot]
level: project
---

# Go API Handler Pattern

## Context

Apply when creating or modifying REST API handlers in Go backend projects.

## Instructions

1. **Handler structure**: Each handler receives a context, binds input, calls service, returns response.
2. **Layered architecture**:
   ```
   Handler → Service → Repository → Database
   ```
3. **Input validation**: Always validate and bind request at the handler level.
4. **Error handling**: Return structured error responses with appropriate HTTP status codes.
5. **Naming convention**:
   - Handlers: `GetUser`, `CreateUser`, `UpdateUser`, `DeleteUser`
   - Services: `UserService` with methods matching handler names
   - Repositories: `UserRepository` interface + `userRepoImpl`

## Examples

### Handler

```go
func (h *UserHandler) GetUser(c *gin.Context) {
    id := c.Param("id")
    
    user, err := h.service.GetUser(c.Request.Context(), id)
    if err != nil {
        c.JSON(http.StatusNotFound, gin.H{"error": "user not found"})
        return
    }
    
    c.JSON(http.StatusOK, user)
}
```

### Service Interface

```go
type UserService interface {
    GetUser(ctx context.Context, id string) (*User, error)
    CreateUser(ctx context.Context, req CreateUserRequest) (*User, error)
}
```

## Anti-patterns

- ❌ Database queries directly in handlers
- ❌ Missing input validation
- ❌ Returning raw error messages to clients (security risk)
- ❌ Not using context propagation
