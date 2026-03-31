# Using Entity Framework Core with SQL Server (ORM)

### Introduction to Entity Framework Core

Entity Framework Core (EF Core) គឺជា Object-Relational Mapper (ORM) ដែលអនុញ្ញាតឱ្យអ្នកអភិវឌ្ឍន៍ធ្វើការប្រតិបត្តិការជាមួយមូលដ្ឋានទិន្នន័យដោយប្រើ objects និង LINQ queries ជំនួសការសរសេរ SQL ដោយផ្ទាល់។ EF Core គាំទ្រ SQL Server, PostgreSQL, MySQL, SQLite, Oracle និងមូលដ្ឋានទិន្នន័យផ្សេងៗទៀត។ វាផ្តល់នូវមុខងារ Change Tracking, Lazy Loading, Eager Loading, Migrations និងការគ្រប់គ្រង database schema ដោយស្វ័យប្រវត្តិ។

### Setting Up Entity Framework Core

1. បង្កើត project ASP.NET Core MVC ឬបន្ថែម EF Core ទៅក្នុង project មានរួចស្រាប់។

2. តំឡើង package NuGet

NuGet packages ដែលត្រូវតំឡើងសម្រាប់ EF Core រួមមាន៖
តាមរយៈ NuGet Package Manager Console:

ចូលទៅក្នុង​ Dependency Manager Console ហើយដំឡើង package

- `Microsoft.EntityFrameworkCore` សម្រាប់ core EF functionalities និង package provider សម្រាប់ SQL Server ដែលមានឈ្មោះ
- `Microsoft.EntityFrameworkCore.SqlServer` និង
- `Microsoft.EntityFrameworkCore.Tools` (សម្រាប់ Migrations)។

តាមរយៈ Command Line Interface (CLI): Terminal

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

### 3. បង្កើត DbContext និង Models

ដើម្បីប្រើ EF Core អ្នកត្រូវបង្កើត class ដែលស្រដៀងនឹង table នៅក្នុង database ហើយបង្កើត DbContext class ដែលតំណាងឱ្យ session ជាមួយ database និងផ្តល់ API សម្រាប់ query និង save data។

1. បង្កើត​ DbContext class នៅក្នុង folder `Data/DbContexts` (បង្កើត folder ប្រសិនបើមិនមាន)

`Data/DbContexts/AppDbContext.cs`

```csharp

using Microsoft.EntityFrameworkCore;
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }

    public DbSet<Category> Categories { get; set; }
    // បន្ថែម DbSet សម្រាប់ tables ផ្សេងៗទៀត
    /// <summary>
    /// Gets or sets the products.
    /// </summary>
    public DbSet<Product> Products { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        // កំណត់ Fluent API configurations ប្រសិនបើចាំបាច់
    }
}

```

2. បង្កើត Model class នៅក្នុង folder `Models`
   `Models/Product.cs`

```csharp
// Sample product model with a navigation property to Category
// If you have already product model, you can skip this step or modify it to fit your existing model
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }  // Navigation property
}
```

3. កំណត់ connection string នៅក្នុង `appsettings.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=WebECommerceDB;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
  // ផ្នែកផ្សេងៗទៀត
}
```

4. កំណត់ DbContext នៅក្នុង `Program.cs`

```csharp

//Inject DbContext into the service container
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

```

5. បង្កើត និងអនុវត្ត Migrations ដើម្បីបង្កើត database schema

តាមរយៈ **Terminal**:

```bash
// បង្កើត Migrations file ឈ្មោះ InitialCreate
dotnet ef migrations add InitialCreate
// អនុវត្ត Migrations ដើម្បីបង្កើត database schema
dotnet ef database update
```

ឬតាមរយៈ **Package Manager Console**:
ចូលទៅក្នុង Tools > NuGet Package Manager > Package Manager Console ហើយបង្កើត Migrations​ file និង update database

```powershell
// បង្កើត Migrations file ឈ្មោះ InitialCreate
PM> Add-Migration InitialCreate
// អនុវត្ត Migrations ដើម្បីបង្កើត database schema
PM> Update-Database
```

បន្ទាប់ពីអនុវត្ត Migrations អ្នកនឹងមាន database និង tables ដែលត្រូវបានបង្កើតដោយស្វ័យប្រវត្តិ។
មើលលទ្ធផលនៅក្នុង SQL Server Management Studio ឬ database explorer ក្នុង Visual Studio ដើម្បីបញ្ជាក់ថា database និង tables ត្រូវបានបង្កើតដោយស្វ័យប្រវត្តិ។

**Visual Studio**:
ចូលទៅក្នុង View > SQL Server Object Explorer ហើយភ្ជាប់ទៅ SQL Server instance របស់អ្នក។ រកមើល database ឈ្មោះ `WebECommerceDB` និងពិនិត្យមើល tables ដូចជា `Products` និង `Categories` ដែលត្រូវបានបង្កើត។

ប្រសិនប្រើ SQL Server Management Studio (SSMS):
ចូលទៅក្នុង SSMS ហើយភ្ជាប់ទៅ SQL Server instance របស់អ្នក។ រកមើល database ឈ្មោះ `WebECommerceDB` និងពិនិត្យមើល tables ដូចជា `Products` និង `Categories` ដែលត្រូវបានបង្កើត។

---

### Controllers និង Views

បន្ទាប់ពីបានកំណត់ DbContext និង Models អ្នកអាចបង្កើត Controllers និង Views ដើម្បីអនុវត្ត CRUD operations ជាមួយ database តាមរយៈ EF Core។
បង្កើត Controller ដោយប្រើ scaffolding ឬដៃដើម្បីបង្កើត Controller និង Views សម្រាប់ Model របស់អ្នក។

```bash
dotnet aspnet-codegenerator controller -name ProductsController -m Product -dc AppDbContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
```

ឬតាមរយៈ Visual Studio: បង្កើត Controller ដោយប្រើ manual empty controller

- ចុចស្ដាំលើ folder Controllers > Add > Controller
- ជ្រើសរើស MVC Controller - Empty > បញ្ចូលឈ្មោះ Controller (e.g., ProductsController) > Add
- បន្ទាប់មកបន្ថែម actions និង views ដើម្បីអនុវត្ត CRUD operations ជាមួយ database តាមរយៈ EF Core។

```csharp
public class ProductsController : Controller
{
    private readonly AppDbContext _context;

    public ProductsController(AppDbContext context)
    {
        _context = context;
    }

    // GET: Products
    public async Task<IActionResult> Index(string? filter=null)
    {
        var products = _context.Products.Include(p => p.Category);
        if (!string.IsNullOrEmpty(filter))
        {
            products = products.Where(p => p.Name.Contains(filter));
        }


        return View(await products.ToListAsync());
    }

    //Details
    public async Task<IActionResult> Details(int? id)
    {
        if (id == null)
        {
            return NotFound();
        }

        var product = await _context.Products
            .Include(p => p.Category)
            .FirstOrDefaultAsync(m => m.Id == id);
        if (product == null)
        {
            return NotFound();
        }

        return View(product);
    }

    // GET: Products/Create
    public IActionResult Create()
    {
        ViewBag.Categories = new SelectList(_context.Categories, "Id", "CategoryName");
        return View();
    }

    // POST: Products/Create
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Create([Bind("Id,Name,Price,CategoryId")] Product product)
    {
        if (ModelState.IsValid)
        {
            _context.Add(product);
            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }
        ViewBag.Categories = new SelectList(_context.Categories, "Id", "CategoryName", product.CategoryId);
        return View(product);
    }


    [HttpGet]
    public async Task<IActionResult> Edit(int? id)
    {
        if (id == null)
        {
            return NotFound();
        }

        var product = await _context.Products.FindAsync(id);
        if (product == null)
        {
            return NotFound();
        }
        ViewBag.Categories = new SelectList(_context.Categories, "Id", "CategoryName", product.CategoryId);
        return View(product);
    }

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Edit(int id, [Bind("Id,Name,Price,CategoryId")] Product product)
    {
        if (id != product.Id)
        {
            return NotFound();
        }

        if (ModelState.IsValid)
        {
            try
            {
                _context.Update(product);
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!ProductExists(product.Id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }
            return RedirectToAction(nameof(Index));
        }
        ViewBag.Categories = new SelectList(_context.Categories, "Id", "CategoryName", product.CategoryId);
        return View(product);
    }

    // Post: Products/Delete/5
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

### Views

បង្កើត Views សម្រាប់ actions ក្នុង Controller ដើម្បីបង្ហាញ និងគ្រប់គ្រង data ពី database តាមរយៈ EF Core។ អ្នកអាចប្រើ scaffolding ដើម្បីបង្កើត Views ឬបង្កើតដោយដៃ។

- ចុចស្ដាំលើ action method (e.g., Index, Create, Edit, Details) > Add View > ជ្រើសរើស template (e.g., List, Create, Edit, Details) > បញ្ចូល model class (e.g., Product) > Add
- បន្ទាប់មកកែសម្រួល Views ដើម្បីបង្ហាញ data ពី database និងបន្ថែម form elements សម្រាប់ Create និង Edit actions។

**Index.cshtml**

```html
@model IEnumerable<Product>
  @{ ViewData["Title"] = "Products"; }
  <h1>Products</h1>
  <p>
    <a asp-action="Create" class="btn btn-primary">Create New</a>
  </p>
  <table class="table">
    <thead>
      <tr>
        <th>Name</th>
        <th>Price</th>
        <th>Category</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      @foreach (var item in Model) {
      <tr>
        <td>@item.Name</td>
        <td>@item.Price</td>
        <td>@item.Category?.CategoryName</td>
        <td>
          <a
            asp-action="Edit"
            asp-route-id="@item.Id"
            class="btn btn-sm btn-warning"
            >Edit</a
          >
          <a
            asp-action="Details"
            asp-route-id="@item.Id"
            class="btn btn-sm btn-info"
            >Details</a
          >
          <form
            asp-action="Delete"
            asp-route-id="@item.Id"
            method="post"
            style="display:inline"
          >
            <button
              type="submit"
              onclick="return confirm('Are you sure you want to delete this product?');"
              class="btn btn-sm btn-danger"
            >
              Delete
            </button>
          </form>
        </td>
      </tr>
      }
    </tbody>
  </table></Product
>
```

**Create.cshtml**

```html
@model Product @{ ViewData["Title"] = "Create Product"; }
<h1>Create Product</h1>
<form asp-action="Create" method="post">
  <div class="form-group">
    <label asp-for="Name"></label>
    <input asp-for="Name" class="form-control" />
    <span asp-validation-for="Name" class="text-danger"></span>
  </div>
  <div class="form-group">
    <label asp-for="Price"></label>
    <input asp-for="Price" class="form-control" />
    <span asp-validation-for="Price" class="text-danger"></span>
  </div>
  <div class="form-group">
    <label asp-for="CategoryId">Category</label>
    <select
      asp-for="CategoryId"
      class="form-control"
      asp-items="ViewBag.Categories"
    >
      <option value="">-- Select Category --</option>
    </select>
    <span asp-validation-for="CategoryId" class="text-danger"></span>
  </div>
  <button type="submit" class="btn btn-primary">Create</button>
  <a asp-action="Index" class="btn btn-secondary">Back to List</a>
</form>
```

**Edit.cshtml**

```html
@model Product @{ ViewData["Title"] = "Edit Product"; }
<h1>Edit Product</h1>
<form asp-action="Edit" method="post">
  <input type="hidden" asp-for="Id" />
  <div class="form-group">
    <label asp-for="Name"></label>
    <input asp-for="Name" class="form-control" />
    <span asp-validation-for="Name" class="text-danger"></span>
  </div>
  <div class="form-group">
    <label asp-for="Price"></label>
    <input asp-for="Price" class="form-control" />
    <span asp-validation-for="Price" class="text-danger"></span>
  </div>
  <div class="form-group">
    <label asp-for="CategoryId">Category</label>
    <select
      asp-for="CategoryId"
      class="form-control"
      asp-items="ViewBag.Categories"
    >
      <option value="">-- Select Category --</option>
    </select>
    <span asp-validation-for="CategoryId" class="text-danger"></span>
  </div>
  <button type="submit" class="btn btn-primary">Save</button>
  <a asp-action="Index" class="btn btn-secondary">Back to List</a>
</form>
```

**Details.cshtml**

```html
@model Product @{ ViewData["Title"] = "Product Details"; }
<h1>Product Details</h1>
<div>
  <h4>Product</h4>
  <hr />
  <dl class="row">
    <dt class="col-sm-2">Name</dt>
    <dd class="col-sm-10">@Model.Name</dd>

    <dt class="col-sm-2">Price</dt>
    <dd class="col-sm-10">@Model.Price</dd>

    <dt class="col-sm-2">Category</dt>
    <dd class="col-sm-10">@Model.Category?.CategoryName</dd>
  </dl>
</div>
<div>
  <a asp-action="Edit" asp-route-id="@Model.Id" class="btn btn-primary">Edit</a>
  <a asp-action="Index" class="btn btn-secondary">Back to List</a>
</div>
```

### Next Steps

- បន្ថែម validation attributes ទៅលើ Model properties ដើម្បីធានាថា data ដែលបញ្ចូលត្រឹមត្រូវ។
- បង្កើត Views សម្រាប់ Category និង tables ផ្សេងៗទៀតដែលអ្នកបានកំណត់ក្នុង DbContext។
- បន្ថែម pagination, sorting និង filtering functionalities នៅក្នុង Index view ដើម្បីធ្វើឱ្យ user experience ល្អប្រសើរឡើង។

### Summary

ក្នុង Lab នេះ យើងបានរៀនពីរបៀបប្រើ Entity Framework Core ជាមួយ SQL Server ដើម្បីអនុវត្ត CRUD operations នៅក្នុង ASP.NET Core MVC application។ យើងបានបង្កើត DbContext និង Models, កំណត់ connection string, បង្កើត និងអនុវត្ត Migrations ដើម្បីបង្កើត database schema, បង្កើត Controllers និង Views ដើម្បីគ្រប់គ្រង data ពី database តាមរយៈ EF Core។

យើងបានឃើញថា EF Core ផ្តល់នូវ features ពេញលេញសម្រាប់ ORM ដែលអនុញ្ញាតឱ្យអ្នកអភិវឌ្ឍន៍ធ្វើការប្រតិបត្តិការជាមួយ database ដោយប្រើ objects និង LINQ queries ជំនួសការសរសេរ SQL ដោយផ្ទាល់។

EF Core គាំទ្រ SQL Server, PostgreSQL, MySQL, SQLite, Oracle និងមូលដ្ឋានទិន្នន័យផ្សេងៗទៀត។ វាផ្តល់នូវមុខងារ Change Tracking, Lazy Loading, Eager Loading, Migrations និងការគ្រប់គ្រង database schema ដោយស្វ័យប្រវត្តិ។
