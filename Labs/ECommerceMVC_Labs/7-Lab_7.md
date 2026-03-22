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
