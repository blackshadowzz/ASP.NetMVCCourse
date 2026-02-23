## **1. Form Submission (POST / GET)**

Form គឺជារបៀបធម្មតា និងសាមញ្ញបំផុតសម្រាប់បញ្ជូនទិន្ន័យពី View ទៅ Controller។

### **View (Razor)**

```html
@model YourApp.Models.Student

<form asp-action="Create" method="post">
  <label>Name:</label>
  <input type="text" name="Name" />

  <label>Age:</label>
  <input type="number" name="Age" />

  <button type="submit">Submit</button>
</form>
```

> **ចំណាំ:**
>
> - `asp-action="Create"` បញ្ជូនទៅ action method `Create` នៅក្នុង controller ។
> - `method="post"` ជាការបញ្ជាក់ HTTP POST request។

---

### **Controller**

```csharp
public class StudentController : Controller
{
    [HttpPost]
    public IActionResult Create(string Name, int Age)
    {
        // អាចប្រើ Name និង Age ដើម្បីបង្កើត object ឬសំរេច logic
        ViewBag.Message = $"Student: {Name}, Age: {Age}";
        return View();
    }
}
```

**ឬប្រើ Model Binding ជាមួយ Model Object:**

```csharp
[HttpPost]
public IActionResult Create(Student student)
{
    // student.Name និង student.Age មានទិន្នន័យពី form
    return View(student);
}
```

---

## **2. URL Parameters / Query String**

### **Example 1: URL Parameters**

```csharp
// URL: /Student/Details/5
public IActionResult Details(int id)
{
    // id = 5
    return View();
}
```

**View Link:**

```html
<a asp-controller="Student" asp-action="Details" asp-route-id="5"
  >View Details</a
>
```

---

### **Example 2: Query String**

```csharp
// URL: /Student/Search?name=Sophea&age=20
public IActionResult Search(string name, int age)
{
    // name = Sophea, age = 20
    return View();
}
```

**View Link:**

```html
<a href="/Student/Search?name=Sophea&age=20">Search Student</a>
```

> **ចំណាំ:**
> Query string និង URL parameters អាចប្រើបានសម្រាប់ GET requests, មិនសមស្របសម្រាប់ទិន្នន័យ sensitive ទេ។

---

## **3. AJAX / Fetch API (JavaScript)**

ការប្រើ **AJAX** ឬ **Fetch API** អាចបញ្ជូនទិន្នន័យទៅ Controller ដោយសិនខ្លួនអ្នកមិនប្តូរទំព័រទេ (asynchronous request)។

### **View (Razor + JavaScript)**

```html
<label>Name:</label>
<input type="text" id="name" />
<label>Age:</label>
<input type="number" id="age" />
<button id="btnSubmit">Submit via AJAX</button>

<script>
  document.getElementById("btnSubmit").addEventListener("click", function () {
    const data = {
      Name: document.getElementById("name").value,
      Age: parseInt(document.getElementById("age").value),
    };

    fetch("/Student/CreateAjax", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        RequestVerificationToken:
          "@Antiforgery.GetAndStoreTokens(HttpContext).RequestToken",
      },
      body: JSON.stringify(data),
    })
      .then((response) => response.json())
      .then((result) => alert(result.message))
      .catch((error) => console.error(error));
  });
</script>
```

> **ចំណាំ:**
>
> - `Content-Type: application/json` ប្រើសម្រាប់ JSON payload
> - ASP.NET Core MVC **ក៏ត្រូវការ AntiForgeryToken** ប្រសិនបើ CSRF protection enabled

---

### **Controller**

```csharp
[HttpPost]
public IActionResult CreateAjax([FromBody] Student student)
{
    // ការទទួល JSON body
    return Json(new { message = $"Student: {student.Name}, Age: {student.Age}" });
}
```

---

## ✅ **សង្ខេប**

| របៀប             | HTTP Method               | តម្រូវការ        | លក្ខណៈ                                  |
| ---------------- | ------------------------- | ---------------- | --------------------------------------- |
| Form             | POST / GET                | name attribute   | Simple, synchronous page reload         |
| URL Parameter    | GET                       | Route parameter  | Use in route, small data                |
| Query String     | GET                       | key=value in URL | Transparent, good for filtering/search  |
| AJAX / Fetch API | POST / GET / PUT / DELETE | JSON payload     | Asynchronous, no page reload, modern UI |

---
