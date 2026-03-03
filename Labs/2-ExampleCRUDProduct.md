

# 🎯 Complete Flow

Example Project: **Product Management System**

---

# 1️⃣ Install EF Core Packages

Open **Package Manager Console** or use **.NET CLI**

## Using .NET CLI

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.Design
```

## Using Package Manager Console

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.EntityFrameworkCore.Design
```

---


# ✅ Project Structure

```
/Controllers
    ProductController.cs
/Models
    Product.cs
/Data
    AppDbContext.cs
/Views
    /Product
        Index.cshtml
        Create.cshtml
        Edit.cshtml
        Details.cshtml
        Delete.cshtml
```

---

# 1️⃣ Model

## `Models/Product.cs`

```csharp
using System.ComponentModel.DataAnnotations;

public class Product
{
    public int Id { get; set; }

    [Required]
    [StringLength(100)]
    public string Name { get; set; }="";

    [Range(0, 999999)]
    public decimal? Price { get; set; }

    [Range(0, 10000)]
    public int? Stock { get; set; }
    public int? CategoryId { get; set; } // Ref to table Categories
}
```

---

# 2️⃣ DbContext

## `Data/AppDbContext.cs`

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public DbSet<Product> Products { get; set; }
}
```

---

# 3️⃣ Register in `Program.cs`

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

---

# 4️⃣ Controller (Full CRUD)

## `Controllers/ProductController.cs`

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

public class ProductController : Controller
{
    private readonly AppDbContext _context;

    public ProductController(AppDbContext context)
    {
        _context = context;
    }

    // READ ALL
    public async Task<IActionResult> Index()
    {
        return View(await _context.Products.ToListAsync());
    }

    // CREATE GET
    public IActionResult Create()
    {
        return View();
    }

    // CREATE POST
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Create(Product product)
    {
        if (ModelState.IsValid)
        {
            _context.Add(product);
            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }
        return View(product);
    }

    // EDIT GET
    public async Task<IActionResult> Edit(int id)
    {
        var product = await _context.Products.FindAsync(id);
        if (product == null) return NotFound();
        return View(product);
    }

    // EDIT POST
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Edit(int id, Product product)
    {
        if (id != product.Id) return NotFound();

        if (ModelState.IsValid)
        {
            _context.Update(product);
            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }
        return View(product);
    }

    // DETAILS
    public async Task<IActionResult> Details(int id)
    {
        var product = await _context.Products.FirstOrDefaultAsync(p => p.Id == id);
        if (product == null) return NotFound();
        return View(product);
    }

    // DELETE GET
    public async Task<IActionResult> Delete(int id)
    {
        var product = await _context.Products.FindAsync(id);
        if (product == null) return NotFound();
        return View(product);
    }

    // DELETE POST
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> DeleteConfirmed(int id)
    {
        var product = await _context.Products.FindAsync(id);
        _context.Products.Remove(product);
        await _context.SaveChangesAsync();
        return RedirectToAction(nameof(Index));
    }
}
```

---

# 5️⃣ Razor Views

All views go inside:

```
Views/Product/
```


* Clean professional appearance

Assumption: Bootstrap 5 is already included in `_Layout.cshtml`.

If not, add inside `<head>`:

```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
```

---


# 📄 Index.cshtml (Styled Table + Header Card)

```html
@model IEnumerable<Product>

<div class="container mt-4">

    <div class="card shadow-sm border-0">
        <div class="card-header bg-primary text-white d-flex justify-content-between align-items-center">
            <h4 class="mb-0">Product Management</h4>
            <a asp-action="Create" class="btn btn-light btn-sm">
                <i class="bi bi-plus-circle"></i> Add Product
            </a>
        </div>

        <div class="card-body">

            <table class="table table-hover table-striped align-middle">
                <thead class="table-dark">
                    <tr>
                        <th>Name</th>
                        <th class="text-end">Price ($)</th>
                        <th class="text-center">Stock</th>
                        <th class="text-center">Actions</th>
                    </tr>
                </thead>
                <tbody>
                @if (!Model.Any())
                {
                    <tr>
                        <td colspan="4" class="text-center text-muted">
                            No products available
                        </td>
                    </tr>
                }
                else
                {
                    @foreach (var item in Model)
                    {
                        <tr>
                            <td>@item.Name</td>
                            <td class="text-end">@item.Price.ToString("N2")</td>
                            <td class="text-center">
                                <span class="badge bg-info text-dark">
                                    @item.Stock
                                </span>
                            </td>
                            <td class="text-center">
                                <a asp-action="Details" asp-route-id="@item.Id"
                                   class="btn btn-sm btn-outline-info">
                                    View
                                </a>

                                <a asp-action="Edit" asp-route-id="@item.Id"
                                   class="btn btn-sm btn-outline-warning">
                                    Edit
                                </a>

                                <a asp-action="Delete" asp-route-id="@item.Id"
                                   class="btn btn-sm btn-outline-danger">
                                    Delete
                                </a>
                            </td>
                        </tr>
                    }
                }
                </tbody>
            </table>

        </div>
    </div>

</div>
```

---

# 📄 Create.cshtml (Professional Form Card)

```html
@model Product

<div class="container mt-4">

    <div class="card shadow border-0">
        <div class="card-header bg-success text-white">
            <h4 class="mb-0">Create Product</h4>
        </div>

        <div class="card-body">

            <form asp-action="Create">

                <div class="row mb-3">
                    <div class="col-md-6">
                        <label asp-for="Name" class="form-label"></label>
                        <input asp-for="Name" class="form-control" placeholder="Enter product name" />
                        <span asp-validation-for="Name" class="text-danger small"></span>
                    </div>

                    <div class="col-md-3">
                        <label asp-for="Price" class="form-label"></label>
                        <input asp-for="Price" class="form-control" />
                    </div>

                    <div class="col-md-3">
                        <label asp-for="Stock" class="form-label"></label>
                        <input asp-for="Stock" class="form-control" />
                    </div>
                </div>

                <div class="d-flex justify-content-between">
                    <a asp-action="Index" class="btn btn-secondary">
                        Back
                    </a>
                    <button type="submit" class="btn btn-success">
                        Save Product
                    </button>
                </div>

            </form>

        </div>
    </div>

</div>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

---

# 📄 Edit.cshtml (Professional Update Form)

```html
@model Product

<div class="container mt-4">

    <div class="card shadow border-0">
        <div class="card-header bg-warning">
            <h4 class="mb-0">Edit Product</h4>
        </div>

        <div class="card-body">

            <form asp-action="Edit">
                <input type="hidden" asp-for="Id" />

                <div class="row mb-3">
                    <div class="col-md-6">
                        <label asp-for="Name" class="form-label"></label>
                        <input asp-for="Name" class="form-control" />
                    </div>

                    <div class="col-md-3">
                        <label asp-for="Price" class="form-label"></label>
                        <input asp-for="Price" class="form-control" />
                    </div>

                    <div class="col-md-3">
                        <label asp-for="Stock" class="form-label"></label>
                        <input asp-for="Stock" class="form-control" />
                    </div>
                </div>

                <div class="d-flex justify-content-between">
                    <a asp-action="Index" class="btn btn-secondary">Back</a>
                    <button type="submit" class="btn btn-warning">Update</button>
                </div>

            </form>

        </div>
    </div>

</div>
```

---

# 📄 Details.cshtml (Card View)

```html
@model Product

<div class="container mt-4">

    <div class="card shadow border-0">
        <div class="card-header bg-info text-white">
            <h4 class="mb-0">Product Details</h4>
        </div>

        <div class="card-body">

            <div class="row mb-3">
                <div class="col-md-4 fw-bold">Name:</div>
                <div class="col-md-8">@Model.Name</div>
            </div>

            <div class="row mb-3">
                <div class="col-md-4 fw-bold">Price:</div>
                <div class="col-md-8">@Model.Price.ToString("N2")</div>
            </div>

            <div class="row mb-3">
                <div class="col-md-4 fw-bold">Stock:</div>
                <div class="col-md-8">@Model.Stock</div>
            </div>

            <a asp-action="Index" class="btn btn-secondary">Back</a>

        </div>
    </div>

</div>
```

---

# 📄 Delete.cshtml (Professional Confirmation)

```html
@model Product

<div class="container mt-4">

    <div class="card shadow border-0">
        <div class="card-header bg-danger text-white">
            <h4 class="mb-0">Delete Confirmation</h4>
        </div>

        <div class="card-body">

            <div class="alert alert-warning">
                Are you sure you want to delete this product?
            </div>

            <dl class="row">
                <dt class="col-sm-3">Name</dt>
                <dd class="col-sm-9">@Model.Name</dd>

                <dt class="col-sm-3">Price</dt>
                <dd class="col-sm-9">@Model.Price</dd>
            </dl>

            <form asp-action="Delete">
                <input type="hidden" asp-for="Id" />

                <button type="submit" class="btn btn-danger">
                    Confirm Delete
                </button>

                <a asp-action="Index" class="btn btn-secondary">
                    Cancel
                </a>
            </form>

        </div>
    </div>

</div>
```

---
