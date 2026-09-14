---
name: api-endpoint
description: How this team adds a Minimal API endpoint. Apply whenever adding, changing or removing an HTTP endpoint.
---

# Adding an endpoint

Follow this procedure whenever the task involves a new or changed HTTP endpoint.
Do not invent a different structure.

## 1. Where things go

| Concern | Location |
|---|---|
| Route handler | `[Project].Api/Endpoints/[Resource]Endpoints.cs` |
| Request and response DTOs | `[Project].Core/Dtos/` |
| Business logic | `[Project].Core/Services/[Resource]Service.cs` |
| Data access | `[Project].Data/Repositories/[Resource]Repository.cs` |
| Tests | `[Project].Tests/Endpoints/[Resource]EndpointTests.cs` |

## 2. Registration

Each resource has one static class exposing one extension method:

```csharp
public static class [Resource]Endpoints
{
    public static IEndpointRouteBuilder Map[Resource]Endpoints(
        this IEndpointRouteBuilder app)
    {
        app.MapGet("/[resource]", HandleListAsync)
           .WithName("List[Resource]")
           .WithOpenApi();
        return app;
    }
}
```

Register it in `Program.cs` with the other `Map*Endpoints()` calls, in
alphabetical order.

## 3. Required elements

Every handler must have all of these:

- A `CancellationToken` parameter, forwarded to every call that accepts one
- `TypedResults` return types, expressed as `Results<Ok<T>, NotFound, BadRequest<string>>`
- Validation before any data access, returning `BadRequest` with a usable message
- A DTO return type. Never an EF entity.
- `.WithName()` and `.WithOpenApi()`
- Rate limiting on anything unauthenticated or expensive

## 4. Layering

The handler calls a service. The service calls a repository. The handler does not
touch `DbContext`. The repository does not know about HTTP.

Repositories return the domain type or `null`. The service decides what "not
found" means. The handler decides what status code that is.

## 5. Tests that come with it

An endpoint is not done until these exist:

- Happy path, asserting status code and response shape
- Each validation failure, asserting the message
- Not-found, where applicable
- Cancellation: assert the token reaches the repository
- One integration test hitting the real route through `WebApplicationFactory`

## 6. Before you say it is finished

Run `dotnet build` and `dotnet test`. If either fails, fix it. Do not report
completion on a red build.
