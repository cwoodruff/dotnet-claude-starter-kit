# [PROJECT NAME]: Claude Code Configuration

> Replace every bracketed placeholder. The file is worth roughly what you put into it.
> Budget twenty minutes. Section 4 is the one that pays you back.

## Project Overview

[Two or three sentences. What this service does, who uses it, what it talks to.]

Stack: C#, .NET [10], ASP.NET Core Minimal APIs, EF Core [10], [SQL Server].

Structure:
- `[Project].Api/`   HTTP surface, endpoint registration, DI composition
- `[Project].Core/`  Entities, DTOs, service interfaces. No EF, no ASP.NET types.
- `[Project].Data/`  DbContext, configurations, repositories, migrations
- `[Project].Tests/` xUnit

`Core` depends on nothing. `Data` depends on `Core`. `Api` depends on both.

## Essential Commands

```bash
dotnet build                                  # build all
dotnet test                                   # full suite
dotnet test --filter "Category=Unit"          # fast tests only
dotnet run --project [Project].Api            # run locally
dotnet ef migrations add <Name> \
  --project [Project].Data \
  --startup-project [Project].Api             # new migration
dotnet ef database update \
  --project [Project].Data \
  --startup-project [Project].Api             # apply migrations
```

## Code Conventions

- Minimal APIs only. No controllers.
- Endpoints live in `[Project].Api/Endpoints/`, one static class per resource,
  registered through a `Map[Resource]Endpoints()` extension method.
- Never return EF entities from an endpoint. Map to a DTO in `[Project].Core/Dtos/`.
- Every public type and member gets an XML documentation comment.
- Every async method accepts a `CancellationToken` and forwards it to every call
  that accepts one.
- Use `TypedResults`, not `Results`, so the OpenAPI shape is inferred.
- Repository methods return domain types or `null`. They do not throw for
  "not found".
- One assertion concept per test. Name tests `Method_Scenario_ExpectedResult`.

## What NOT to Do

> This is the section everyone skips and the one that changes the output most.
> Each line below should be a bug someone already shipped. Add yours.

- Do not modify files under `Migrations/` by hand. Generate a new migration.
- Do not register `AnthropicClient`, `HttpClient`, or anything holding a socket
  as Scoped or Transient. Singleton, or via `IHttpClientFactory`.
- Do not capture a scoped service (`DbContext`) inside a singleton.
- Do not commit connection strings, API keys, or tokens. Use User Secrets locally
  and the key vault in deployed environments.
- Do not write `async void`. Return `Task`.
- Do not access navigation properties inside a loop without an `.Include()`.
- Do not add a NuGet package without asking first.
- Do not change the public shape of an existing endpoint without asking first.

## Working Agreement

- Read the relevant files before proposing a change. If you cannot see it, do not
  assume it.
- For anything touching more than three files, describe the plan and wait for
  approval before executing.
- After each file write the build runs automatically (see `.claude/settings.json`).
  If it fails, fix your own error before continuing.
- When you are uncertain, say so plainly rather than guessing confidently.
