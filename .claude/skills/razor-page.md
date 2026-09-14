---
name: razor-page
description: How this team adds or changes an ASP.NET Core Razor Page. Apply whenever the task involves a .cshtml page, a PageModel, a form post, or page-level authorization.
---

# Adding a Razor Page

Follow this procedure whenever the task involves a new or changed Razor Page.
Do not invent a different structure.

## 1. Where things go

| Concern | Location |
|---|---|
| Page markup | `[Project].Web/Pages/[Area]/[Name].cshtml` |
| Page logic | `[Project].Web/Pages/[Area]/[Name].cshtml.cs` |
| Shared markup | `[Project].Web/Pages/Shared/_[Name].cshtml` |
| Markup with logic | `[Project].Web/ViewComponents/` |
| Business logic | `[Project].Core/Services/` |
| Data access | `[Project].Data/Repositories/` |
| Tests | `[Project].Tests/Pages/[Name]PageTests.cs` |

The folder structure under `Pages/` **is** the URL. `Pages/Invoices/Edit.cshtml`
serves `/Invoices/Edit`. Do not add routing attributes to work around this.

## 2. The PageModel shape

```csharp
public class EditModel(IInvoiceService invoices) : PageModel
{
    [BindProperty]
    public InputModel Input { get; set; } = new();

    public class InputModel
    {
        [Required, StringLength(70)]
        public string BillingAddress { get; set; } = string.Empty;

        [Range(0, 100000)]
        public decimal Total { get; set; }
    }

    public async Task<IActionResult> OnGetAsync(int id, CancellationToken ct)
    {
        var invoice = await invoices.GetAsync(id, ct);
        if (invoice is null) return NotFound();

        Input = new InputModel
        {
            BillingAddress = invoice.BillingAddress,
            Total = invoice.Total
        };
        return Page();
    }

    public async Task<IActionResult> OnPostAsync(int id, CancellationToken ct)
    {
        if (!ModelState.IsValid) return Page();

        var updated = await invoices.UpdateAsync(id, Input.BillingAddress, Input.Total, ct);
        if (!updated) return NotFound();

        return RedirectToPage("./Index");
    }
}
```

## 3. Required elements

Every page must have all of these:

- **A nested `InputModel`.** Never bind `[BindProperty]` to an EF entity. Binding
  to an entity is how overposting happens: the model binder will happily set any
  property the form names, including ones you never put on the form.
- **A `CancellationToken` parameter** on every async handler, forwarded onward.
- **`ModelState.IsValid` checked before any write**, returning `Page()` on failure
  so the user keeps their input.
- **Post, Redirect, Get.** A successful `OnPost` returns `RedirectToPage()`.
  Returning `Page()` on success means a refresh resubmits the form.
- **Validation attributes on the InputModel**, plus `<div asp-validation-summary>`
  and `<span asp-validation-for>` in the markup.
- **Tag helpers**, not `Html.*` helpers.

## 4. Antiforgery

Razor Pages validates the antiforgery token automatically on POST, and the form
tag helper emits it automatically. Two ways teams break this, both of which you
should refuse to do:

- Applying `IgnoreAntiforgeryToken` to make something work.
- Posting via `fetch` or `XMLHttpRequest` without sending the token.

For an AJAX post, read the token and send it in the `RequestVerificationToken`
header. Do not disable the check.

## 5. Layering

The PageModel calls a service. The service calls a repository. The PageModel does
not touch `DbContext`, and the `.cshtml` file does not call anything.

If a `.cshtml` file needs data it does not have, the answer is a property on the
PageModel or a view component, never a service call in the markup.

## 6. Authorization

Prefer conventions in `Program.cs`:

```csharp
builder.Services.AddRazorPages(options =>
{
    options.Conventions.AuthorizeFolder("/Invoices");
    options.Conventions.AllowAnonymousToPage("/Index");
});
```

Scattered `[Authorize]` attributes are hard to audit. A convention block is one
place to read.

## 7. Tests that come with it

A page is not done until these exist:

- `OnGetAsync` happy path, asserting the returned `IActionResult` type and that
  `Input` is populated
- `OnGetAsync` not-found, asserting `NotFoundResult`
- `OnPostAsync` with invalid `ModelState`, asserting it returns `PageResult` and
  does not call the service
- `OnPostAsync` happy path, asserting `RedirectToPageResult` and the target
- One integration test through `WebApplicationFactory` that requests the real URL
  and asserts a 200 and an expected element in the HTML

To test an invalid model, add the error yourself:
`pageModel.ModelState.AddModelError("Input.Total", "required");`

## 8. Before you say it is finished

Run `dotnet build` and `dotnet test`. The build compiles `.cshtml` files, so a
markup error fails the build. If either fails, fix it. Do not report completion on
a red build.
