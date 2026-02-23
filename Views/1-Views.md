
# 3. Views

**View** គឺជាឯកសារដែលអ្នកប្រើប្រាស់មើលទិន្នន័យ (data) តាម browser, ជាឧបករណ៍សម្រាប់បង្ហាញ **Model**។
**View File** មានឈ្មោះបញ្ចប់ `*.cshtml` សម្រាប់ C#។

---

## 3.1. ទីតាំងសម្រាប់ទុក Views

View files អាចស្ថិតនៅក្នុង៖

```
Views/<ControllerName>/ViewName.cshtml
Views/Shared/ViewName.cshtml
```

* គ្រប់ **Views** ត្រូវស្ថិតនៅក្នុងថត `Views/<ControllerName>` ឬ `Views/Shared`។
* Views ដែលបង្ហាញ markup ត្រូវមាន extension `*.cshtml`។
* View អាចបង្កើតបានពីរបៀប៖

  1. បង្កើតចេញពី **action method** របស់ **controller**
  2. បង្កើតដោយផ្ទាល់ក្នុងថតរង (controller sub-directory)

---

## 3.2. Convention-based View Resolution

ASP.NET MVC ប្រើ **convention-based rules** ដើម្បីស្វែងរក view:

* **Controller name → View folder name**
  ឧទាហរណ៍:

  ```
  ProductController → /Views/Product/
  HomeController → /Views/Home/
  ```

* **Action method name → View file name**
  ឧទាហរណ៍៖

  ```
  Index() → /Views/Product/Index.cshtml
  Details() → /Views/Product/Details.cshtml
  ```

---

## 3.3. Wrong Naming Convention

**ករណីខុស**៖

```
ProductController → /Views/Products/
Index() → /Views/Product/Index.cshtml
```

**ដំណោះស្រាយ**៖

1. Rename folder/file to follow convention:

   ```
   /Views/Product/Index.cshtml
   ```
2. Or explicitly specify path in controller:

   ```csharp
   return View("~/Views/Products/Index.cshtml");
   ```

---

## 3.4. Best Practices

* **Action method name** ត្រូវតែស្របនឹង view file name
* **Controller name** ត្រូវតែស្របនឹង view folder name

---

## 3.5. MVC Resolution for Nested Folders

By default:

```
ProductController.Index() → /Views/Product/Index.cshtml
```

* បើមាន **subfolder** នៅក្នុង `/Views/Product/`, ឧទាហរណ៍ `/Views/Product/Reports/`
  MVC មិនស្វែងរក automatically ទៅកាន់ subfolder ទេ
* ត្រូវ **explicitly specify** path:

  ```csharp
  // Looks inside /Views/Product/Reports/Report.cshtml
  return View("Reports/Report");
  ```

---

## 3.6. Example

**Controller Example**:

```csharp
public class ProductController : Controller
{
    // Looks for /Views/Product/Index.cshtml
    public IActionResult Index()
    {
        return View();
    }

    // Looks for /Views/Product/Reports/Report.cshtml
    public IActionResult Report()
    {
        return View("Reports/Report");
    }

    // Explicit path
    public IActionResult Custom()
    {
        return View("~/Views/Shared/CustomView.cshtml");
    }
}
```

**Folder Structure**:

```
Views/
├─ Product/
│  ├─ Index.cshtml
│  ├─ Details.cshtml
│  └─ Reports/
│      └─ Report.cshtml
└─ Shared/
   └─ CustomView.cshtml
```

---



# 3.2.1. ទម្រង់ View (View Templates)

**View Templates** គឺជាទម្រង់ដែលកំណត់ប្រភេទ view ដែលអ្នកចង់បង្កើត។ មានប្រភេទពេញនិយមដូចជា៖

| ទម្រង់                  | ពន្យល់                                                                                        |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| **Empty (គ្មាន Model)** | បង្កើត view ដោយមិនត្រូវការប្រើ **model class**។ ប្រើសម្រាប់ទំព័រស្ថិតស្ថេរ ឬ custom content ។ |
| **Create**              | បង្កើត view សម្រាប់បញ្ចូលទិន្នន័យតាម **model object**។                                        |
| **Delete**              | បង្កើត view សម្រាប់លុបទិន្នន័យ។                                                               |
| **Details**             | បង្កើត view សម្រាប់បង្ហាញព័ត៌មានលំអិតពី **model class**។                                      |
| **Edit**                | បង្កើត view សម្រាប់កែទិន្នន័យពី **model class**។                                              |
| **List**                | បង្កើត view សម្រាប់បង្ហាញទិន្នន័យទាំងអស់ពី **model class** (ជាទូទៅប្រើសម្រាប់ Index page)។    |

**ឧទាហរណ៍ (Product Model)**:

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

* ប្រសិនបើអ្នកចង់បង្ហាញ **List of Products** → ជ្រើស `List` template
* ប្រសិនបើអ្នកចង់បញ្ចូល Product ថ្មី → ជ្រើស `Create` template

---

# 3.2.2. ការបង្កើត View ចេញពី Action Method របស់ Controller

ដំណាក់កាល៖

1. ជ្រើស **Controller** ក្នុង **Solution Explorer**
2. ចុច **Right-click** លើ **Action Method** ដែលអ្នកចង់បង្កើត View
3. ជ្រើស **Add → View**
4. កំណត់៖

   * **View template** (Empty, Create, Edit, Details, List...)
   * **Model class** (បើចាំបាច់)
   * **Layout page** (បើចង់ប្រើ)
5. ចុច **Add** → View ត្រូវបានបង្កើតនៅ **Views/<ControllerName>/ViewName.cshtml** ដោយស្វ័យប្រវត្តិ

---

**ឧទាហរណ៍ Controller**៖

```csharp
public class ProductController : Controller
{
    // បង្ហាញ list of products
    public IActionResult Index()
    {
        List<Product> products = new List<Product>
        {
            new Product { Id=1, Name="Laptop", Price=1500 },
            new Product { Id=2, Name="Phone", Price=800 }
        };
        return View(products); // ប្រើ List template
    }

    // បញ្ចូល product ថ្មី
    public IActionResult Create()
    {
        return View(); // ប្រើ Create template
    }

    // បង្ហាញព័ត៌មានលំអិត
    public IActionResult Details(int id)
    {
        Product product = new Product { Id=id, Name="Laptop", Price=1500 };
        return View(product); // ប្រើ Details template
    }
}
```

**Folder Structure**:

```
Views/
├─ Product/
│  ├─ Index.cshtml      // List template
│  ├─ Create.cshtml     // Create template
│  ├─ Details.cshtml    // Details template
```

---


