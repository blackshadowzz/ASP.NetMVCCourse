
# SQL Server Connection String Options & EF Core Configuration

## 1. `TrustServerCertificate=true`

### Definition

`TrustServerCertificate=true` is a SQL Server connection string parameter used in ADO.NET, Entity Framework Core, JDBC, ODBC, and other SQL Server clients.

It controls how the client validates the SQL Server’s TLS/SSL certificate during encrypted connections.

---

### Behavior

When set to `true`:

* The client **does NOT validate** the SQL Server TLS/SSL certificate.
* The connection **can still be encrypted** if `Encrypt=true` is enabled.
* Certificate authenticity is **not verified against a trusted Certificate Authority (CA)**.

When set to `false` (recommended for production):

* The client validates the certificate.
* The certificate must be trusted by the OS or installed in the Trusted Root store.

---

### Security Implications

⚠ **Not recommended for production environments**

Reason:

* Vulnerable to **Man-in-the-Middle (MITM)** attacks.
* No guarantee you are connecting to the intended SQL Server.
* Breaks end-to-end trust validation.

---

### When to Use

✔ Development
✔ Local testing
✔ Internal lab environments
✖ Production

---

### Related Parameter: `Encrypt`

Modern .NET (6/7/8+) defaults to:

```text
Encrypt=True
```

If:

* `Encrypt=True`
* `TrustServerCertificate=False`

Then a valid SSL certificate is required.

---

## 2. `MultipleActiveResultSets=true` (MARS)

### Definition

`MultipleActiveResultSets=true` enables **MARS (Multiple Active Result Sets)** in SQL Server.

It allows multiple SQL commands to execute on the same connection simultaneously.

---

### Without MARS

You cannot execute another query while a `SqlDataReader` is still open:

```csharp
// This will throw exception if MARS is disabled
var reader1 = command1.ExecuteReader();
var reader2 = command2.ExecuteReader(); 
```

Error:

> There is already an open DataReader associated with this Connection

---

### With MARS Enabled

You can:

* Execute nested queries
* Use multiple DataReaders
* Perform lazy loading (EF6 scenarios)
* Avoid opening multiple database connections

---

### When to Use

✔ Applications executing nested queries
✔ EF6 lazy loading
✔ Complex transactional operations

✖ High-performance systems with strict connection control (can introduce overhead)

---

### Performance Note

MARS:

* Slightly increases memory usage
* May reduce predictability in high-load systems
* Is not required in most EF Core scenarios

EF Core generally does not require MARS unless manually managing multiple readers.

---



## 3. Typical SQL Server Connection Strings

### Remote SQL Server

```text
Server=myServer;
Database=myDB;
User Id=myUser;
Password=myPass;
Encrypt=true;
TrustServerCertificate=true;
```

---

### LocalDB (Development)

```text
Server=(localdb)\mssqllocaldb;
Database=MVCDbTest;
Trusted_Connection=True;
MultipleActiveResultSets=true;
TrustServerCertificate=true;
```

---

### Explanation of Common Parameters

| Parameter                | Meaning                      |
| ------------------------ | ---------------------------- |
| Server                   | SQL Server instance name     |
| Database                 | Target database              |
| User Id                  | SQL authentication username  |
| Password                 | SQL authentication password  |
| Trusted_Connection=True  | Use Windows Authentication   |
| Encrypt                  | Enables TLS encryption       |
| TrustServerCertificate   | Skips certificate validation |
| MultipleActiveResultSets | Enables MARS                 |

---

# 2️⃣ Configuring `DbContext` in EF Core (Two Ways)

Assume we have:

```csharp
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

## ✅ Method 1: Configure Connection String in `Program.cs`

### Step 1: Add Connection String in `appsettings.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=MVCDbTest;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=true;"
  }
}
```

---

### Step 2: Inject DbContext in `Program.cs`

(.NET 6 / 7 / 8 Minimal Hosting Model)

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();
```

✔ Recommended approach
✔ Best practice for production
✔ Supports multiple environments

---

## ✅ Method 2: Override `OnConfiguring()` Inside DbContext

You can configure the connection directly inside `AppDbContext`.

```csharp
public class AppDbContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        if (!options.IsConfigured)
        {
            options.UseSqlServer(
                "Server=(localdb)\\mssqllocaldb;Database=MVCDbTest;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=true;");
        }
    }

    public DbSet<Product> Products { get; set; }
}
```

### When to Use

✔ Small projects
✔ Console apps
✔ Quick testing

✖ Not recommended for large production systems
✖ Hardcoded connection string

---

## 🆚 Comparison

| Feature                        | Program.cs Configuration | OnConfiguring() |
| ------------------------------ | ------------------------ | --------------- |
| Clean Architecture             | ✔                        | ✖               |
| Supports Multiple Environments | ✔                        | ✖               |
| Production Ready               | ✔                        | ⚠ Limited       |
| Test Friendly                  | ✔                        | ✖               |

✔ Best Practice: Use `Program.cs` configuration.

---

# 3️⃣ Injecting DbContext into Controller

Example:

```csharp
public class ProductController : Controller
{
    private readonly AppDbContext _context;

    public ProductController(AppDbContext context)
    {
        _context = context;
    }

    public IActionResult Index()
    {
        var products = _context.Products.ToList();
        return View(products);
    }
}
```

EF Core uses **Dependency Injection (DI)** from ASP.NET Core’s built-in IoC container.

---

# 4️⃣ Entity Framework Core Migration Commands

Migrations allow you to:

* Version control database schema
* Automatically create/update database structure
* Sync C# models with SQL tables

---

## Using .NET CLI

### Add Migration

```bash
dotnet ef migrations add MigrationName
```

Example:

```bash
dotnet ef migrations add InitialCreate
```

---

### Update Database

```bash
dotnet ef database update
```

---

## Using Package Manager Console (Visual Studio)

### Add Migration

```powershell
Add-Migration MigrationName
```

### Update Database

```powershell
Update-Database
```

---

## Useful Advanced Commands

### Remove Last Migration

```bash
dotnet ef migrations remove
```

### Generate SQL Script

```bash
dotnet ef migrations script
```

Useful for:

* Production deployment
* DBA review
* CI/CD pipelines