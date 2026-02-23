# 1. Model (ម៉ូឌែល)

**Definition:**
Model គឺជា **C# class (`*.cs`)** ដែលមាន **Properties** និង **business logic** ដែលផ្ទុកទិន្ន័យ (data) របស់កម្មវិធី។

- Model មួយអាចតំណាងឲ្យ **តារាងក្នុង Database** (optional).
- **Properties** និមួយៗនៃ Model ត្រូវគ្នា (map) ជាមួយ **columns** នៃតារាងក្នុង Database បើកំពុងប្រើ ORM (Entity Framework Core).
- Model អាចមាន **methods** សម្រាប់ការគណនា ឬ business logic ផ្សេងៗ.

---

### Example in C#

```csharp
using System.ComponentModel.DataAnnotations;

public class Student
{
    [Key]
    public int Id { get; set; }             // maps to Id column in DB
    [Required]
    public string Name { get; set; }        // maps to Name column
    public int Age { get; set; }            // maps to Age column

    // Example of business logic inside Model
    public bool IsAdult()
    {
        return Age >= 18;
    }
}
```

**Explanation:**

- `Student` គឺជា Model.
- `Id`, `Name`, និង `Age` គឺជា **Properties**.
- Method `IsAdult()` គឺជា **business logic**.
- បើប្រើ EF Core, `Student` Model នេះអាចតំណាងឲ្យ table `Students` ក្នុង database.

---

## 1.1 បង្កើត Model

**Key points:**

- Models ត្រូវរក្សាទុកក្នុងថត **Models** (Views និង Controllers គ្មានទាក់ទង).
- Class name ត្រូវផ្គូរផ្គងជាមួយ **entity name** (ឧ. table name ប្រសិនបើ map to DB).
- Properties និមួយៗត្រូវ map ជាមួយ column ក្នុង database (optional).

---

### Example 1: Student Model

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApp.Models
{
    public class Student
    {
        [Key] // primary key
        public int Id { get; set; }

        [Required(ErrorMessage = "សូមបញ្ចូលឈ្មោះ")]
        public string Name { get; set; }

        public int Age { get; set; }

        // Business logic example
        public bool IsAdult()
        {
            return Age >= 18;
        }
    }
}
```

---

### Example 2: Subject Model

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApp.Models
{
    public class Subject
    {
        [Key]
        public int Id { get; set; }

        [Required]
        public string Name { get; set; }

        public int CreditHours { get; set; }
    }
}
```

---

### Notes:

1. ទាំងពីរ Models (`Student` និង `Subject`) ត្រូវរក្សាទុកក្នុងថត `Models`.
2. Property `Id` គឺជា primary key ដោយប្រើ `[Key]`.
3. អាចបន្ថែម **business logic methods** នៅក្នុង Model ដើម្បីធ្វើការគណនា ឬ validate ទិន្ន័យ.
4. ពេលប្រើ Entity Framework Core, Model នេះអាច map ទៅ **tables** `Students` និង `Subjects`.

---

ចាស់! ខាងក្រោមនេះជាការពន្យល់លម្អិតសម្រាប់ **ការបញ្ជូន Model ពី Controller ទៅ View** ក្នុង ASP.NET Core MVC ជាមួយ **syntax និងឧទាហរណ៍**។

---

## 2. ការបញ្ជូន Model ពី Controller ទៅ View

នៅក្នុង ASP.NET Core MVC, មានវិធីសាស្ត្រចម្បង 3 ដើម្បីផ្ញើទិន្ន័យពី **Controller → View**:

1. **ViewBag** – dynamic object, flexible, no compile-time checking.
2. **ViewData** – dictionary, key-value pair, slightly less convenient than ViewBag.
3. **Passing Model directly using `View()` helper method** – recommended for strongly typed Views.

---

### 2.1 Syntax

```csharp
// Passing a single model object
return View(model);

// Passing model with a specific view name
return View("ViewName", model);
```

**Model type:**

- **Single object:** ex: `Student`
- **Collection object:** ex: `List<Student>`

---

### Example 1: Single object Model

**Controller (StudentsController.cs):**

```csharp id="student-single"
using Microsoft.AspNetCore.Mvc;
using MyApp.Models;

public class StudentsController : Controller
{
    public IActionResult Details()
    {
        var student = new Student
        {
            Id = 1,
            Name = "Sophea",
            Age = 20
        };

        // Pass single object to view
        return View(student);
    }
}
```

**View (Details.cshtml):**

```razor id="student-single-view"
@model MyApp.Models.Student

<h2>Student Details</h2>
<p>Id: @Model.Id</p>
<p>Name: @Model.Name</p>
<p>Age: @Model.Age</p>
<p>Is Adult? @(Model.IsAdult() ? "Yes" : "No")</p>
```

---

### Example 2: Collection object Model

**Controller (StudentsController.cs):**

```csharp id="student-collection"
using Microsoft.AspNetCore.Mvc;
using MyApp.Models;

public class StudentsController : Controller
{
    public IActionResult Index()
    {
        var students = new List<Student>
        {
            new Student { Id = 1, Name = "Sophea", Age = 20 },
            new Student { Id = 2, Name = "Vichea", Age = 17 },
            new Student { Id = 3, Name = "Sreyneang", Age = 22 }
        };

        // Pass collection to view
        return View(students);
    }
}
```

**View (Index.cshtml):**

```razor id="student-collection-view"
@model IEnumerable<MyApp.Models.Student>

<h2>Students List</h2>
<table class="table table-bordered">
    <thead>
        <tr>
            <th>Id</th>
            <th>Name</th>
            <th>Age</th>
            <th>Adult?</th>
        </tr>
    </thead>
    <tbody>
    @foreach(var student in Model)
    {
        <tr>
            <td>@student.Id</td>
            <td>@student.Name</td>
            <td>@student.Age</td>
            <td>@(student.IsAdult() ? "Yes" : "No")</td>
        </tr>
    }
    </tbody>
</table>
```

---

### Notes:

1. `@model` directive in Razor View is **strongly typed**.
2. Passing **single object** vs **collection** only changes how you loop or access properties.
3. Using `ViewBag` or `ViewData` is less type-safe but can be used for small data snippets:

```csharp
ViewBag.Message = "Hello Students!";
ViewData["Count"] = students.Count;
```

Then in View:

```razor
<p>@ViewBag.Message</p>
<p>Total Students: @ViewData["Count"]</p>
```

---

## 3. ការបង្ហាញ Model លើ View

**`@model` Directive**

- ប្រើសម្រាប់ **ប្រកាសប្រភេទ Model** ដែលបានបញ្ជូនពី **Controller → View** ឬ **View → Controller Action parameter**។
- Model ដែលបានបញ្ជូនគឺជាអថេរ (variable) នៅក្នុង View ដែលអាច access **Properties និង Methods** របស់ Model។

---

### 3.1. Usage of `@model` directive

**Syntax:**

```razor
@model ClassName          // single object
@model IEnumerable<ClassName>  // collection object
@model List<ClassName>         // collection object
```

- **Single object:** ប្រើសម្រាប់ Model object តែមួយ។
- **Collection object:** ប្រើសម្រាប់ Model ដែលជា List, Array ឬ IEnumerable។

---

### Example 1: Single object Model

**Controller:**

```csharp id="single-model-controller"
public IActionResult Details()
{
    var student = new Student
    {
        Id = 1,
        Name = "Sophea",
        Age = 20
    };

    return View(student); // passing single object
}
```

**View (Details.cshtml):**

```razor id="single-model-view"
@model MyApp.Models.Student

<h2>Student Details</h2>
<p>Id: @Model.Id</p>
<p>Name: @Model.Name</p>
<p>Age: @Model.Age</p>
<p>Adult? @(Model.IsAdult() ? "Yes" : "No")</p>
```

**Explanation:**

- `@model MyApp.Models.Student` បញ្ជាក់ថា View នេះទទួល **single Student object**.
- `@Model` គឺជាអថេរ access Properties និង Methods របស់ Student.

---

### Example 2: Collection object Model

**Controller:**

```csharp id="collection-model-controller"
public IActionResult Index()
{
    var students = new List<Student>
    {
        new Student { Id = 1, Name = "Sophea", Age = 20 },
        new Student { Id = 2, Name = "Vichea", Age = 17 },
        new Student { Id = 3, Name = "Sreyneang", Age = 22 }
    };

    return View(students); // passing collection
}
```

**View (Index.cshtml):**

```razor id="collection-model-view"
@model IEnumerable<MyApp.Models.Student>

<h2>Students List</h2>
<table class="table table-bordered">
    <thead>
        <tr>
            <th>Id</th>
            <th>Name</th>
            <th>Age</th>
            <th>Adult?</th>
        </tr>
    </thead>
    <tbody>
    @foreach(var student in Model)
    {
        <tr>
            <td>@student.Id</td>
            <td>@student.Name</td>
            <td>@student.Age</td>
            <td>@(student.IsAdult() ? "Yes" : "No")</td>
        </tr>
    }
    </tbody>
</table>
```

**Explanation:**

- `@model IEnumerable<MyApp.Models.Student>` បញ្ជាក់ថា View នេះទទួល **collection of Student objects**.
- `@foreach(var student in Model)` loop តាម **each student** ក្នុង collection.

---

### Notes:

1. `@model` ជា **strongly-typed declaration**, type-safe និងមាន IntelliSense support.
2. Recommended to use **@model + View(model)** instead of `ViewBag`/`ViewData` when passing main data objects.
3. Can work with **single object** or **collection object** depending on your Controller logic.

---
