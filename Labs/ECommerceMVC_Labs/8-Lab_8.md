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
