# សប្តាហ៍ទី៤៖ `Layout`, `Tag Helpers` និង `Form Handling`

ការកែប្រែ `Layout`, `Tag Helpers` និង `Form Handling`

នៅសប្តាហ៍នេះ យើងនឹងផ្តោតលើការធ្វើឱ្យ **UI** របស់ Project ឲ្យកាន់តែល្អ និងមានភាពស៊ីសង្វាក់គ្នា រួមទាំងការបង្កើត/កែប្រែ **Category** ដោយប្រើ **Form** និង **Tag Helpers**។

## ១. កែប្រែ Layout (\_Layout.cshtml)

Layout គឺជា “គ្រោងឆ្អឹង” រួម (shared template) ដែលបង្ហាញនៅគ្រប់ទំព័រ។  
ឯកសារ: **Views/Shared/\_Layout.cshtml**

[មើលមេរៀន​​ Page Layout](../../Views/5-Page_Layout.md)

#### ឧទាហរណ៍កែប្រែ \_Layout.cshtml (បន្ថែម Navbar សាមញ្ញ)

```html
<!DOCTYPE html>
<html lang="km">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - E-Commerce</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
  </head>
  <body>
    <header>
      <nav class="navbar navbar-expand-sm navbar-dark bg-dark box-shadow mb-3">
        <div class="container">
          <a
            class="navbar-brand"
            asp-area=""
            asp-controller="Home"
            asp-action="Index"
            >E-Commerce</a
          >
          <button
            class="navbar-toggler"
            type="button"
            data-bs-toggle="collapse"
            data-bs-target=".navbar-collapse"
          >
            <span class="navbar-toggler-icon"></span>
          </button>
          <div
            class="navbar-collapse collapse d-sm-inline-flex justify-content-between"
          >
            <ul class="navbar-nav flex-grow-1">
              <li class="nav-item">
                <a
                  class="nav-link text-light"
                  asp-area=""
                  asp-controller="Home"
                  asp-action="Index"
                  >Home</a
                >
              </li>
              <li class="nav-item">
                <a
                  class="nav-link text-light"
                  asp-area=""
                  asp-controller="Categories"
                  asp-action="Index"
                  >Categories</a
                >
              </li>
            </ul>
          </div>
        </div>
      </nav>
    </header>

    <div class="container">
      <main role="main" class="pb-3">@RenderBody()</main>
    </div>

    <footer class="border-top footer text-muted">
      <div class="container">&copy; 2025 - E-Commerce</div>
    </footer>

    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>

    @await RenderSectionAsync("Scripts", required: false)
  </body>
</html>
```

**ចំណុចសំខាន់**

- `asp-controller` និង `asp-action` → នេះគឺជា **Tag Helpers**
- ប្រើ Bootstrap 5 (តាម default របស់ ASP.NET Core MVC template)

---

### ២. Tag Helpers សំខាន់ៗដែលត្រូវប្រើ

| Tag Helper               | ប្រើសម្រាប់                       | ឧទាហរណ៍ក្នុង Layout/Form                       |
| ------------------------ | --------------------------------- | ---------------------------------------------- |
| `asp-controller`         | បញ្ជាក់ controller ដែលត្រូវទៅ     | `asp-controller="Categories"`                  |
| `asp-action`             | បញ្ជាក់ action method             | `asp-action="Index"`                           |
| `asp-route-id`           | បញ្ជូន id តាម route               | `asp-route-id="@item.Id"`                      |
| `asp-for`                | ភ្ជាប់ជាមួយ model property (form) | `asp-for="Name"`                               |
| `asp-validation-for`     | បង្ហាញ validation message         | `asp-validation-for="Name"`                    |
| `asp-validation-summary` | បង្ហាញកំហុសទាំងអស់                | `<div asp-validation-summary="ModelOnly" ...>` |

---

### ៣. បង្កើត Form សម្រាប់ Create Category

#### ជំហានទី១៖ បន្ថែម Action Create ក្នុង CategoriesController

```csharp
// GET: បង្ហាញ Form ទទេ
[HttpGet]
public IActionResult Create()
{
    return View();
}

// POST: ទទួលទិន្នន័យពី Form
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult Create(Category category)
{
    if (ModelState.IsValid)
    {
        categories.Add(category);
        TempData["Success"] = $"បានបន្ថែមប្រភេទ '{category.Name}' រួចរាល់!";
        return RedirectToAction(nameof(Index));
    }
    return View(category);
}
```

---

#### ជំហានទី២៖ បង្កើត View Create.cshtml (Views/Categories/Create.cshtml)

```html
@model Category @{ ViewData["Title"] ="New Category"; }

<h1>@ViewData["Title"]</h1>

<hr />

<div class="row">
  <div class="col-md-6">
    <form asp-action="Create" method="post">
      <div asp-validation-summary="ModelOnly" class="text-danger"></div>

      <div class="form-group mb-3">
        <label asp-for="Name" class="control-label"></label>
        <input asp-for="Name" class="form-control" />
        <span asp-validation-for="Name" class="text-danger"></span>
      </div>

      <div class="form-group mb-3">
        <label asp-for="Description" class="control-label"></label>
        <textarea
          asp-for="Description"
          class="form-control"
          rows="4"
        ></textarea>
        <span asp-validation-for="Description" class="text-danger"></span>
      </div>

      <div class="form-group">
        <input type="submit" value="Create" class="btn btn-primary" />
        <a asp-action="Index" class="btn btn-secondary">Back</a>
      </div>
    </form>
  </div>
</div>

@section Scripts {
<partial name="_ValidationScriptsPartial" />
}
```

---

### ៤. កែប្រែ Index.cshtml ដើម្បីមានតំណ “បន្ថែមប្រភេទថ្មី”

បន្ថែមក្នុង **Index.cshtml** (ក្នុង `<div class="mb-3">`)

```html
<a asp-action="Create" class="btn btn-success">បន្ថែមប្រភេទថ្មី</a>
```

---

### ៥. កិច្ចការ Lab សប្តាហ៍ទី៤

1. កែ **\_Layout.cshtml** និងបំបែក Navbar ទៅជា Partial view ផ្សេង Ex: `_Navbar.cshtml`
2. បន្ថែម Action **Create**, **Edit**, **Delete** (GET + POST) និង View **Create.cshtml**
3. បន្ថែមការបង្ហាញសារជោគជ័យ (ជាមួយ TempData) នៅក្នុង Index.cshtml  
   ឧទាហរណ៍:

```html
@if (TempData["Success"] != null) {
<div class="alert alert-success alert-dismissible fade show" role="alert">
  @TempData["Success"]
  <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
</div>
}
```

4. Run សាកល្បងបន្ថែម Category ថ្មីមួយ ហើយមើលថាតើត្រឡប់មក Index ដោយបង្ហាញសារជោគជ័យឬអត់
