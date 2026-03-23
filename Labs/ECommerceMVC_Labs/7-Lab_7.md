# Using Dapper with SQL Server

Dapper គឺជា **Micro ORM** (Micro Object-Relational Mapper) ដ៏ពេញនិយមបំផុតមួយសម្រាប់ .NET។ វាមិនមែនជា ORM ពេញលេញដូច Entity Framework ទេ (គ្មាន Change Tracking, Migration ស្វ័យប្រវត្តិ, LINQ ជាដើម) ប៉ុន្តែវាលឿនខ្លាំង ស្រាល និងផ្តល់សិទ្ធិគ្រប់គ្រង SQL ទាំងស្រុងដល់ developer។

Dapper បន្ថែម **extension methods** លើ `IDbConnection` (ពី ADO.NET) ដើម្បីធ្វើការ execute SQL និង map លទ្ធផលទៅជា object ឬ list របស់ C# យ៉ាងងាយស្រួល។

វាត្រូវបានបង្កើតឡើងដោយក្រុម `Stack Overflow` ដើម្បីដោះស្រាយបញ្ហា performance នៅពេលប្រើ ORM ធម្មតា។

---

### លក្ខណៈសំខាន់ៗរបស់ Dapper

- លឿនជាង Entity Framework ច្រើន (ជិតដូច ADO.NET សុទ្ធ)
- សរសេរ SQL ដោយផ្ទាល់ → គ្មាន "surprise query" ដូច EF
- គាំទ្រ multi-mapping, stored procedure, transaction, multiple result sets
- គាំទ្រ SQL Server, PostgreSQL, MySQL, SQLite, Oracle ជាដើម

### ចាប់ផ្តើមពី Zero ដល់ CRUD ជាមួយ SQL Server

#### 1. រៀបចំ Project

បង្កើត ASP.NET Core MVC បើមិនទាន់មាន

#### 2. ដំឡើង Package ចាំបាច់

```bash
dotnet add package Dapper
dotnet add package Microsoft.Data.SqlClient   # សម្រាប់ SQL Server
```

#### 3. បង្កើត Table នៅ SQL Server or LocalDB

```sql
CREATE DATABASE WebECommerceDB;
GO

USE WebECommerceDB;
GO

CREATE TABLE Categories (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    CategoryName NVARCHAR(100) NOT NULL,
    [Description] NVARCHAR(250) NULL,
    IsActive bit NOT NULL default 1,
    CreatedDate DATETIME2 NOT NULL DEFAULT GETUTCDATE()
);

```

#### 4. បង្កើត Model (C# class)

```csharp
// Models/Category.cs
public class Category
{
    public int Id { get; set; }
    public string CategoryName { get; set; } = string.Empty;
    public string? Description { get; set; }
    public bool IsActive { get; set; }
    public DateTime CreatedDate { get; set; }
}

```

---

## Connection management service class

the most common and clean approaches in ASP.NET Core MVC are:

1. Hard-coded connection string (quick for development/testing)
2. From appsettings.json (modern style, requires a few extra lines)

---

### Create Connection service

នៅក្នុង​ project root បង្កើត folder ឈ្មោះថា `Data` នៅក្នុង Data folder បង្កើត folder បន្ថែម `Dappers`

ProjectName: - Data/Dappers - IDapperConnection.cs - DapperConnection.cs

1. នៅក្នុង interface IDapperConnection.cs
   សរសេរកូដដូចខាងក្រោម៖

```csharp
using System.Data; //need this namespace

namespace WebAppECommerce.Data.Dappers //change project name base on your project
{
    public interface IDapperConnection
    {
        public IDbConnection CreateConnection();
    }
}


```

2. នៅក្នុង class DapperConnection.cs
   សរសេរកូដដូចខាងក្រោម៖

```csharp
using Microsoft.Data.SqlClient;
using System.Data;

namespace WebAppECommerce.Data.Dappers
{
    public class DapperConnection : IDapperConnection
    {
        private readonly string _connectionString;
        public DapperConnection(IConfiguration configuration)
        {
            _connectionString = configuration.GetConnectionString("DefaultConnection") ?? string.Empty;
        }
        public IDbConnection CreateConnection()
        {
            return new SqlConnection(_connectionString);
        }
    }
}


```

---

### Config connection in appsettings.json

ចូលទៅ​ file **appsettings.json** on project root

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=WebECommerceDB;Trusted_Connection=True;TrustServerCertificate=True;"
  }
}
```

### Inject service to Program.cs

នៅក្នុង Program.cs បន្ថែម Service ដូចខាងក្រោម៖

```csharp

 builder.Services.AddControllersWithViews(); //already exist

 builder.Services.AddScoped<IDapperConnection, DapperConnection>(); // Add this code

```

---

### ការ​ Inject connection to Controller

ឧទាហរណ៍៖ បង្កើត CategoryController.cs បើមិនទាន់មាន

```csharp

using Microsoft.AspNetCore.Mvc;
using WebAppECommerce.Data.Dappers;

namespace WebAppECommerce.Controllers
{
    public class CategoriesController(IDapperConnection dapper) : Controller
    {

    }
}
```

#### `Index` action method

```csharp
public async Task<ActionResult<List<Category>>> Index(string? filter = "")
{
    using var connection = dapper.CreateConnection();
    string sql = "SELECT * FROM Categories";
    var categories = await connection.QueryAsync<Category>(sql);
    if (!string.IsNullOrEmpty(filter))
    {
        categories = categories.Where(c =>
        c.CategoryName.Contains(filter,
        StringComparison.OrdinalIgnoreCase));
    }
    return Ok(categories.ToList());
}

```

#### `Details` action method

```csharp
public async Task<IActionResult> Details(int id)
{
    using var connection = dapper.CreateConnection();
    string sql = "SELECT * FROM Categories WHERE Id = @Id";
    var category = await connection.QueryFirstOrDefaultAsync<Category>(sql, new { Id = id });
    if (category == null) return NotFound();
    return View(category);
}
```

#### `Create` action method

```csharp
public IActionResult Create()
{
    return View();
}
```

Return Id after insert

```csharp
[HttpPost]
public async Task<IActionResult> Create(Category category)
{
    if (ModelState.IsValid)
    {
        using var connection = dapper.CreateConnection();
        string sql = @"INSERT INTO Categories (CategoryName, Description, IsActive)
        VALUES (@CategoryName, @Description, @IsActive)";
        var id = await connection.QuerySingleAsync<int>(sql, category);
        return RedirectToAction(nameof(Index));
    }
    return View(category);
}
```

#### `Edit` action method

```csharp
public async Task<IActionResult> Edit(int id)
{
    using var connection = dapper.CreateConnection();
    string sql = "SELECT * FROM Categories WHERE Id = @Id";
    var category = await connection.QueryFirstOrDefaultAsync<Category>(sql, new { Id = id });
    if (category == null) return NotFound();
    return View(category);
}
```

```csharp
[HttpPost]
public async Task<IActionResult> Edit(int id, Category category)
{
    if (id != category.Id) return BadRequest();
    if (ModelState.IsValid)
    {
        using var connection = dapper.CreateConnection();
        string sql = @"UPDATE Categories SET CategoryName = @CategoryName,
        Description = @Description, IsActive = @IsActive WHERE Id = @Id";
        await connection.ExecuteAsync(sql, category);
        return RedirectToAction(nameof(Index));
    }
    return View(category);
}
```

#### `Delete` action method

```csharp
[HttpPost, ActionName("Delete")]
public async Task<IActionResult> DeleteConfirmed(int id)
{
    using var connection = dapper.CreateConnection();
    string sql = "DELETE FROM Categories WHERE Id = @Id";
    await connection.ExecuteAsync(sql, new { Id = id });
    return RedirectToAction(nameof(Index));
}
```

---

### Views

បង្កើត Views សម្រាប់ Create, Edit, Delete, Index ដូចធម្មតា (Razor syntax) ហើយប្រើ Bootstrap សម្រាប់ styling បើចង់បាន។

Views:

- Views/Categories/Index.cshtml
- Views/Categories/Create.cshtml
- Views/Categories/Edit.cshtml
- Views/Categories/Details.cshtml

### Summary

- Dapper គឺជា Micro ORM ដែលមានល្បឿនខ្លាំង និងស្រាល
- Dapper ផ្តល់សិទ្ធិគ្រប់គ្រង SQL ទាំងស្រុងដល់ developer
- Dapper គាំទ្រ SQL Server, PostgreSQL, MySQL, SQLite, Oracle
- Dapper អាចប្រើសម្រាប់ CRUD operations យ៉ាងងាយស្រួល
- Dapper គ្មាន features ពេញលេញដូច Entity Framework (គ្មាន Change Tracking, Migration ស្វ័យប្រវត្តិ, LINQ ជាដើម) ប៉ុន្តែវាលឿនខ្លាំង និងស្រាល

### Next Steps: Using Entity Framework Core with SQL Server (ORM)

នៅក្នុង Lab 8 យើងនឹងសិក្សាអំពី Entity Framework Core ដែលជាផ្នែក ORM ពេញលេញសម្រាប់ .NET។ វាផ្តល់ features ដូចជា Change Tracking, Migrations, LINQ, និងការគ្រប់គ្រង database schema ដោយស្វ័យប្រវត្តិ។ យើងនឹងបង្កើត CRUD operations ដូចគ្នា ប៉ុន្តែប្រើ EF Core ជំនួស Dapper។
