## 6. ការបញ្ជូនទិន្ន័យពី Action ទៅ View

នៅក្នុង **ASP.NET MVC**, មានវិធីចម្បង 3 ដើម្បីផ្ញើទិន្ន័យពី Controller ទៅ View៖

1. **ViewBag**
2. **ViewData**
3. **TempData**

### 6.1 ViewBag

- **ViewBag** ជា **dynamic property** ដែលអាចផ្ទុកតម្លៃប្រភេទណាក៏បាន។
- វាជាសាមញ្ញសម្រាប់ផ្ញើទិន្ន័យពី controller ទៅ view មួយដង។
- **សម្គាល់**: ViewBag មានអាយុជីវិត **ត្រឹម request មួយ** តែប៉ុណ្ណោះ។

**កំណត់តម្លៃក្នុង Controller:**

```csharp
public IActionResult Index()
{
    ViewBag.Title = "Home Page";
    ViewBag.Categories = new List<string> { "Math", "Physics", "Chemistry" };
    return View();
}
```

**ទាញទិន្ន័យក្នុង View (Razor):**

```razor
<h1>@ViewBag.Title</h1>

<ul>
@foreach(var category in ViewBag.Categories)
{
    <li>@category</li>
}
</ul>
```

---

### 6.2 ViewData

- **ViewData** ជា property មួយរបស់ Controller ដែលផ្ទុកទិន្ន័យជាប្រភេទ **key-value** (dictionary)។
- តម្លៃទទួលបានត្រូវបំលែង (cast) ទៅប្រភេទដែលចាំបាច់មុនប្រើប្រាស់។
- អាយុជីវិតដូចជា **ViewBag** (request scope only)។

**កំណត់តម្លៃក្នុង Controller:**

```csharp
public IActionResult Index()
{
    ViewData["Title"] = "Home Page";
    ViewData["Categories"] = new List<string> { "Math", "Physics", "Chemistry" };
    return View();
}
```

**ទាញទិន្ន័យក្នុង View:**

```razor
<h1>@ViewData["Title"]</h1>

<ul>
@{
    var categories = ViewData["Categories"] as List<string>;
    foreach(var category in categories)
    {
        <li>@category</li>
    }
}
</ul>
```

> **ចំណាំ:** `ViewBag` និង `ViewData` គឺជាវិធីសាមញ្ញសម្រាប់ផ្ញើទិន្ន័យ **ពី Controller ទៅ View** request មួយប៉ុណ្ណោះ។

---

### 6.3 TempData

- **TempData** គឺផ្ទុកទិន្ន័យ **ចម្ងាយពី request មួយទៅ request បន្ទាប់**។
- ប្រើសម្រាប់បង្ហាញ **success/error messages** បន្ទាប់ពី redirect។
- វាផ្ទុកតម្លៃជាប្រភេទ key-value ដូចជា ViewData។

**កំណត់ក្នុង Controller (បន្ទាប់ពី Create action):**

```csharp
public IActionResult Create()
{
    // ការបង្កើតអត្ថបទ ឬ entity មួយ
    TempData["Success"] = "Created successfully!";
    return RedirectToAction("Index");
}
```

**ទាញក្នុង View (បន្ទាប់ពី Redirect):**

```razor
@if(TempData["Success"] != null)
{
    <div class="alert alert-success">
        @TempData["Success"]
    </div>
}
```

---

### 6.4 សង្ខេបប្រៀបធៀប

| Feature  | Type       | Lifetime          | Requires Cast | Typical Use Case                                  |
| -------- | ---------- | ----------------- | ------------- | ------------------------------------------------- |
| ViewBag  | Dynamic    | Request only      | No            | Simple data, one-way binding                      |
| ViewData | Dictionary | Request only      | Yes           | Key-value data, when dynamic typing not preferred |
| TempData | Dictionary | Multiple requests | Yes           | Flash messages after Redirect                     |

---

#### 6.5 Example: Controller + View

**Controller:**

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        ViewBag.Title = "Welcome to My Site";
        ViewData["Categories"] = new List<string> { "Math", "Physics", "Chemistry" };
        TempData["Notice"] = "You are logged in!";
        return View();
    }
}
```

**View (Index.cshtml):**

```razor
<h1>@ViewBag.Title</h1>

<ul>
@{
    var categories = ViewData["Categories"] as List<string>;
    foreach(var category in categories)
    {
        <li>@category</li>
    }
}
</ul>

@if(TempData["Notice"] != null)
{
    <div class="alert alert-info">
        @TempData["Notice"]
    </div>
}
```

---

## 7. SelectList និង DropDownList ជាមួយ ViewBag / ViewData

### 7.1 កំណត់ SelectList ក្នុង Controller

**Controller Example:**

```csharp id="m8f2kq"
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;

public class StudentController : Controller
{
    public IActionResult Create()
    {
        // បង្កើត List ប្រភេទ string
        var categories = new List<string> { "Math", "Physics", "Chemistry" };

        // បង្កើត SelectList និងផ្ញើទៅ View តាម ViewBag
        ViewBag.Categories = new SelectList(categories);

        // ចំលើយទៀតប្រើ ViewData
        ViewData["Subjects"] = new SelectList(categories);

        return View();
    }
}
```

> **ចំណាំ:** `SelectList` គឺជា object ដែល MVC ប្រើសម្រាប់បង្កើត dropdown options។

---

### 7.2 បង្ហាញ DropDownList ក្នុង View

**Razor View (Create.cshtml):**

#### ប្រើ ViewBag

```razor id="vbg1"
<form asp-action="Create" method="post">
    <label for="category">Category:</label>
    @Html.DropDownList("Category", (SelectList)ViewBag.Categories, "Select a category", new { @class = "form-control" })

    <button type="submit" class="btn btn-primary mt-2">Submit</button>
</form>
```

- `"Category"` → field name / model binding name
- `(SelectList)ViewBag.Categories` → cast to SelectList
- `"Select a category"` → default placeholder

#### ប្រើ ViewData

```razor id="vd1"
<form asp-action="Create" method="post">
    <label for="subject">Subject:</label>
    @Html.DropDownList("Subject", (SelectList)ViewData["Subjects"], "Select a subject", new { @class = "form-control" })
</form>
```

---

### 7.3 ប្រើ DropDownListFor ជាមួយ Model

Model `Student`:

```csharp id="mdl1"
public class Student
{
    public string Name { get; set; }
    public string Category { get; set; }  // selected value
}
```

**Controller:**

```csharp id="ctrl1"
public IActionResult Create()
{
    var categories = new List<string> { "Math", "Physics", "Chemistry" };
    ViewBag.Categories = new SelectList(categories);
    return View(new Student());
}
```

**View (strongly typed Razor View):**

```razor id="view1"
@model Student

<form asp-action="Create" method="post">
    <label>Name:</label>
    <input asp-for="Name" class="form-control" />

    <label>Category:</label>
    @Html.DropDownListFor(model => model.Category, (SelectList)ViewBag.Categories, "Select a category", new { @class = "form-control" })

    <button type="submit" class="btn btn-success mt-2">Submit</button>
</form>
```

- `DropDownListFor` → bind selected value directly to model property (`Category`)
- ការជ្រើសរើស dropdown នឹងត្រូវរក្សាទុកនៅក្នុង model ក្នុង post back

---

### 7.4 សង្ខេប

| Feature         | Description                                                |
| --------------- | ---------------------------------------------------------- |
| SelectList      | Object សម្រាប់ផ្ទុក list of options (string, int, objects) |
| DropDownList    | Helper របស់ Razor, ប្រើ ViewBag/ViewData                   |
| DropDownListFor | Helper ប្រើសម្រាប់ strongly typed model binding            |

> Tip: ប្រសិនបើចង់បង្ហាញ **id / value / text** ច្បាស់លាស់ អ្នកអាចប្រើ `new SelectList(items, "Value", "Text")` ជាមួយ object list ផង។

---

💡 **Example Advanced SelectList**:

```csharp id="adv1"
var subjects = new List<Subject> {
    new Subject { Id = 1, Name = "Math" },
    new Subject { Id = 2, Name = "Physics" }
};
ViewBag.Subjects = new SelectList(subjects, "Id", "Name");
```

**View:**

```razor id="adv2"
@Html.DropDownListFor(m => m.SelectedSubjectId, (SelectList)ViewBag.Subjects, "Select a subject", new { @class = "form-control" })
```

- `SelectedSubjectId` → property in model to store the selected **Id**
- `"Id"` → value for option
- `"Name"` → display text

---

## HTML `<select>`

---

## 1️⃣ Model

```csharp
public class Subject
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student
{
    public string Name { get; set; }
    public int Age { get; set; }
    public int SubjectId { get; set; } // stores selected Subject Id
}
```

---

## 2️⃣ Controller

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;

public class StudentController : Controller
{
    public IActionResult Create()
    {
        // List of Subject objects
        var subjects = new List<Subject> {
            new Subject { Id = 1, Name = "Math" },
            new Subject { Id = 2, Name = "Physics" },
            new Subject { Id = 3, Name = "Chemistry" }
        };

        // Send to view via ViewBag
        ViewBag.Subjects = new SelectList(subjects, "Id", "Name");

        return View();
    }

    [HttpPost]
    public IActionResult Create(string Name, int Age, int SubjectId)
    {
        // You now have the selected SubjectId
        TempData["Success"] = $"Student {Name} selected subject with ID {SubjectId} successfully!";
        return RedirectToAction("Create");
    }
}
```

---

## 3️⃣ View (Create.cshtml) – Plain HTML Select

```razor
@{
    ViewBag.Title = "Create Student";
}

<h2>@ViewBag.Title</h2>

@if(TempData["Success"] != null)
{
    <div class="alert alert-success">@TempData["Success"]</div>
}

<form asp-action="Create" method="post">
    <div class="mb-3">
        <label for="Name" class="form-label">Name:</label>
        <input type="text" class="form-control" id="Name" name="Name" placeholder="Enter name" />
    </div>

    <div class="mb-3">
        <label for="Age" class="form-label">Age:</label>
        <input type="number" class="form-control" id="Age" name="Age" placeholder="Enter age" />
    </div>

    <div class="mb-3">
        <label for="SubjectId" class="form-label">Select Subject:</label>
        <select class="form-select" id="SubjectId" name="SubjectId" aria-label="Select Subject">
            <option selected disabled>Open this select menu</option>
            @foreach (var item in (SelectList)ViewBag.Subjects)
            {
                <option value="@item.Value">@item.Text</option>
            }
        </select>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

---

### ✅ Key Points

1. **SelectList from objects** → `new SelectList(subjects, "Id", "Name")`
   - `"Id"` → value of `<option>`
   - `"Name"` → text displayed in dropdown

2. **Plain HTML `<select>`** → cast ViewBag to `SelectList` and loop:

```razor
@foreach (var item in (SelectList)ViewBag.Subjects)
{
    <option value="@item.Value">@item.Text</option>
}
```

3. **Bootstrap styling** → `class="form-select"` for nice dropdown appearance.

4. **Posting** → `name="SubjectId"` ensures the selected value binds to the controller parameter.
