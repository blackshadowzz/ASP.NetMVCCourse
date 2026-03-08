# Introduction to ASP.NET Core MVC

### ១. តើ MVC Architecture ជាអ្វី?

MVC គឺជា software design pattern (គំរូរចនាសម្ព័ន្ធកម្មវិធី) ដែលបែងចែកកម្មវិធីទៅជា ៣ ផ្នែកសំខាន់ៗ ដើម្បីធ្វើឱ្យកូដងាយយល់ ងាយកែប្រែ (maintenance) និងអាចអភិវឌ្ឍបានលឿន។

MVC បែងចែកកម្មវិធីជា ៣ ផ្នែក៖

- **Model** → ទិន្នន័យ + ច្បាប់អាជីវកម្ម (ឧ. class Category)
- **View** → ផ្ទៃប្រើប្រាស់ (បង្ហាញតារាង Category)
- **Controller** → ទទួលសំណើ (request handler) → ទាញ Model → បញ្ជូនទៅ View

**ឧទាហរណ៍ E-Commerce**  
User ចុច `/Categories` → Controller ទាញបញ្ជី Category → View បង្ហាញជាតារាង (ឧ. Electronics, Clothing, Books...)

---

### ២. រចនាសម្ព័ន្ធ Project

```
ECommerceApp/
├── wwwroot/ (css, js, images)
├── Controllers/
│   └── CategoriesController.cs
├── Models/
│   └── Category.cs
├── Views/
│   └── Categories/
│       └── Index.cshtml
└── Program.cs
```

---

### ៣. Lab សប្តាហ៍ទី៣ – បង្កើត CategoriesController + View

#### ជំហានទី១៖ បង្កើត Project ថ្មី (បើមិនទាន់មាន)

1. Visual Studio 2022, 2026 → Create a new project
2. ជ្រើស **ASP.NET Core Web App (Model-View-Controller)**
3. ដាក់ឈ្មោះ **ECommerceApp** → .NET 9.0, 10.0 → Create

#### ជំហានទី២៖ បង្កើត Model Category (សាមញ្ញសិន)

1. ចុចខាងស្តាំ **Models** folder → Add → Class
2. ដាក់ឈ្មោះ **Category.cs**

```csharp
namespace ECommerceApp.Models
{
    public class Category
    {
        public int Id { get; set; }
        public string Name { get; set; } = string.Empty;
        public string Description { get; set; } = string.Empty;
    }
}
```

---

#### ជំហានទី៣៖ បង្កើត CategoriesController

1. ចុចខាងស្តាំ **Controllers** → Add → Controller
2. ជ្រើស **MVC Controller – Empty** → ដាក់ឈ្មោះ **CategoriesController**

```csharp
using Microsoft.AspNetCore.Mvc;
using ECommerceApp.Models;  // បន្ថែម namespace របស់ Model

namespace ECommerceApp.Controllers
{
    public class CategoriesController : Controller
    {
        // បញ្ជីសាកល្បង (សិន មិនទាន់ប្រើ Database)
        private List<Category> _categories = new List<Category>
        {
            new Category { Id = 1, Name = "គ្រឿងអេឡិចត្រូនិក", Description = "ទូរស័ព្ទ កុំព្យូទ័រ" },
            new Category { Id = 2, Name = "សម្លៀកបំពាក់", Description = "អាវ ខោ ស្បែកជើង" },
            new Category { Id = 3, Name = "សៀវភៅ", Description = "ប្រលោមលោក សៀវភៅសិក្សា" }
        };

        // Action បង្ហាញបញ្ជី Category
        public IActionResult Index()
        {
            return View(_categories);  // បញ្ជូន List<Category> ទៅ View
        }
    }
}
```

---

#### ជំហានទី៤៖ បង្កើត View Index.cshtml សម្រាប់ Categories

1. ចុចខាងស្តាំ **Views** folder → Add → New Folder → ដាក់ឈ្មោះ **Categories**  
   (ត្រូវតែដូចឈ្មោះ Controller ដោយគ្មាន "Controller")

2. ចុចខាងស្តាំ folder **Categories** → Add → View
   - View name: **Index**
   - Template: **List** (ជ្រើស Model class → Category)
   - ឬ Empty (បើចង់សរសេរដោយខ្លួនឯង)

**Index.cshtml** (Views/Categories/Index.cshtml) – ឧទាហរណ៍សាមញ្ញ

```html
@model IEnumerable<ECommerceApp.Models.Category>
  @{ ViewData["Title"] = "បញ្ជីប្រភេទផលិតផល"; }

  <h1 class="text-center">@ViewData["Title"]</h1>

  <p>
    <a asp-action="Create" class="btn btn-success">បន្ថែមប្រភេទថ្មី</a>
  </p>

  <table class="table table-bordered table-hover">
    <thead class="table-dark">
      <tr>
        <th>ល.រ</th>
        <th>ឈ្មោះប្រភេទ</th>
        <th>ពិពណ៌នា</th>
        <th>សកម្មភាព</th>
      </tr>
    </thead>
    <tbody>
      @foreach (var item in Model) {
      <tr>
        <td>@item.Id</td>
        <td>@item.Name</td>
        <td>@item.Description</td>
        <td>
          <a asp-action="Edit" asp-route-id="@item.Id">កែ</a> |
          <a asp-action="Details" asp-route-id="@item.Id">មើលលម្អិត</a> |
          <a asp-action="Delete" asp-route-id="@item.Id">លុប</a>
        </td>
      </tr>
      }
    </tbody>
  </table></ECommerceApp.Models.Category
>
```

#### ជំហានទី៥៖ Run និងសាកល្បង

1. ចុច **F5** ឬ Run
2. ចូលទៅ URL:  
   `https://localhost:xxxx/Categories` ឬ `https://localhost:xxxx/Categories/Index`

→ នឹងឃើញតារាងបញ្ជីប្រភេទផលិតផល ៣ ជួរ (ពី List សាកល្បង)

### កិច្ចការ Homework (សប្តាហ៍នេះ)

1. បន្ថែម Action ថ្មី ២ គឺ **Details** និង **Create** (គ្រាន់តែបង្ហាញ Form សាមញ្ញសិន)
2. បង្កើត **\_Layout.cshtml** បើមិនទាន់មាន (កែប្រែ navbar ដាក់តំណ “Categories”)
