# [PROJECT NAME] - Claude Code Configuration

> Replace every bracketed placeholder. The file is worth roughly what you put into it.
> Budget twenty minutes. Section 4 is the one that pays you back.
>
> This template covers both **Razor Pages** and **HTTP API** projects. Most .NET
> solutions have one or both. Delete the convention block you do not use.

## Project Overview

[Two or three sentences. What this application does, who uses it, what it talks to.]

Stack: C#, .NET [10], ASP.NET Core [Razor Pages and/or Minimal APIs], EF Core [10], [SQL Server].

Structure:
- `[Project].Web/`   Razor Pages UI. `Pages/`, `wwwroot/`, view components.
- `[Project].Api/`   HTTP surface. Minimal API endpoints.
- `[Project].Core/`  Entities, DTOs, service interfaces. No EF, no ASP.NET types.
- `[Project].Data/`  DbContext, configurations, repositories, migrations.
- `[Project].Tests/` xUnit.

`Core` depends on nothing. `Data` depends on `Core`. `Web` and `Api` depend on both.
Neither `Web` nor `Api` references the other.

## Essential Commands

```bash
dotnet build                                  # build all
dotnet test                                   # full suite
dotnet test --filter "Category=Unit"          # fast tests only
dotnet run --project [Project].Web            # run the UI
dotnet run --project [Project].Api            # run the API
dotnet ef migrations add <Name> \
  --project [Project].Data \
  --startup-project [Project].Api             # new migration
dotnet ef database update \
  --project [Project].Data \
  --startup-project [Project].Api             # apply migrations
```

## Code Conventions

### Shared

- Every public type and member gets an XML documentation comment.
- Every async method accepts a `CancellationToken` and forwards it to every call
  that accepts one.
- Business logic lives in `[Project].Core/Services/`. Neither a PageModel nor an
  endpoint handler contains business rules.
- Repository methods return domain types or `null`. They do not throw for
  "not found".
- One assertion concept per test. Name tests `Method_Scenario_ExpectedResult`.

### Razor Pages (`[Project].Web`)

- Pages live in `Pages/` and the folder structure mirrors the URL.
- One `PageModel` per page. Handlers are `OnGetAsync`, `OnPostAsync`, and named
  handlers like `OnPostDeleteAsync` invoked with `asp-page-handler`.
- **Never bind to an EF entity.** Declare a nested `InputModel` class and bind to
  that. This is the overposting defense, not a style preference.
- `[BindProperty]` for form input. `[BindProperty(SupportsGet = true)]` only when
  a GET genuinely needs it.
- Check `ModelState.IsValid` before any write. Return `Page()` on failure so the
  user keeps their input and sees the validation summary.
- Post, Redirect, Get. A successful `OnPost` ends in `RedirectToPage()`, never in
  a rendered `Page()`.
- Tag helpers (`asp-for`, `asp-page`, `asp-route-*`) over `Html.*` helpers.
- Shared usings and tag helper registrations go in `_ViewImports.cshtml`.
- `.cshtml` files contain markup and display logic only. No data access, no
  service calls, no conditionals that encode business rules.
- Reusable markup is a partial. Reusable markup that needs its own logic is a
  view component.
- Authorize by convention in `Program.cs` (`AuthorizeFolder`, `AuthorizePage`)
  rather than scattering `[Authorize]`, unless the page is a genuine exception.

### HTTP API (`[Project].Api`)

- Minimal APIs only. No controllers.
- Endpoints live in `Endpoints/`, one static class per resource, registered
  through a `Map[Resource]Endpoints()` extension method.
- Never return EF entities. Map to a DTO in `[Project].Core/Dtos/`.
- Use `TypedResults`, not `Results`, so the OpenAPI shape is inferred.
- Validate before any data access and return `BadRequest` with a usable message.

## What NOT to Do

> This is the section everyone skips and the one that changes the output most.
> Each line below should be a bug someone already shipped. Add yours.

### Shared

- Do not modify files under `Migrations/` by hand. Generate a new migration.
- Do not register `AnthropicClient`, `HttpClient`, or anything holding a socket
  as Scoped or Transient. Singleton, or via `IHttpClientFactory`.
- Do not capture a scoped service (`DbContext`) inside a singleton.
- Do not commit connection strings, API keys, or tokens. Use User Secrets locally
  and the key vault in deployed environments.
- Do not write `async void`. Return `Task`.
- Do not access navigation properties inside a loop without an `.Include()`.
- Do not add a NuGet package without asking first.

### Razor Pages

- Do not bind a form directly to an EF entity. Use an `InputModel`.
- Do not call `IgnoreAntiforgeryToken` or disable antiforgery to make an AJAX
  post work. Send the token instead.
- Do not use `@Html.Raw()` on anything a user can influence.
- Do not put `DbContext` or a repository call inside a `.cshtml` file.
- Do not return `Page()` from a successful POST. Redirect.
- Do not use `Redirect()` with a user-supplied URL. Use `LocalRedirect()`.
- Do not put sensitive data in `TempData` or `ViewData`.

### HTTP API

- Do not return EF entities from an endpoint.
- Do not change the public shape of an existing endpoint without asking first.

## Working Agreement

- Read the relevant files before proposing a change. If you cannot see it, do not
  assume it.
- For anything touching more than three files, describe the plan and wait for
  approval before executing.
- After each file write the build runs automatically (see `.claude/settings.json`).
  If it fails, fix your own error before continuing.
- When you are uncertain, say so plainly rather than guessing confidently.
