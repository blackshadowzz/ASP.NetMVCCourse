ខាងក្រោមនេះជាការពន្យល់លម្អិតអំពី **Model Binding ក្នុង ASP.NET Core MVC** ជាមួយនឹង **ឧទាហរណ៍កូដ** ដើម្បីអោយងាយយល់។

---

# 5. Model Binding (ASP.NET Core MVC)

**Model Binding** គឺជាប្រព័ន្ធមួយក្នុង ASP.NET Core MVC ដែលធ្វើការបំលែង និងផ្គូរផ្គង **ទិន្នន័យពី HTTP Request** ទៅកាន់ **parameters របស់ Action Method នៅក្នុង Controller** ដោយស្វ័យប្រវត្តិ។

នៅពេលដែល User ផ្ញើ Request មក Server (តាម Form, URL, Query String)
ASP.NET Core MVC នឹងយកតម្លៃទាំងនោះទៅបំពេញទៅក្នុង **parameters ឬ Model objects** នៃ Action Method។

### ប្រភពទិន្នន័យដែល Model Binding អាចយកបាន

ASP.NET Core MVC អាច bind ទិន្នន័យពី៖

1. **Form Data** – ទិន្នន័យពី HTML Form
2. **Route Data** – ទិន្នន័យពី URL Routing
3. **Query String** – ទិន្នន័យនៅក្នុង URL parameter

ឧទាហរណ៍ URL៖

```
https://example.com/Product/Details?id=5
```

* `id=5` គឺជា **Query String**
* MVC នឹង bind តម្លៃនេះទៅ parameter `id`

---

# 5.1 Simple Type Binding

**Simple Type Binding** គឺជាការផ្គូរផ្គងទិន្នន័យពី HTTP Request ទៅកាន់ **primitive type parameters** នៅក្នុង Action Method។

### Primitive Types ឧទាហរណ៍

* `string`
* `int`
* `bool`
* `decimal`
* `DateTime`

### លក្ខខណ្ឌសំខាន់

ឈ្មោះ **Request Key** ត្រូវតែដូចគ្នានឹង **Parameter Name** នៅក្នុង Action Method។

---

## Example 1: Binding From Query String

### URL

```
https://localhost:5001/Product/Details?id=10
```

### Controller

```csharp
public class ProductController : Controller
{
    public IActionResult Details(int id)
    {
        ViewBag.ProductId = id;
        return View();
    }
}
```

### Explanation

* Query String: `id=10`
* MVC bind `10` → parameter `id`
* Action method ទទួលបាន `id = 10`

---

## Example 2: Binding From Form Data

### View (HTML Form)

```html
<form asp-action="Login" method="post">
    <input type="text" name="username" />
    <input type="password" name="password" />
    <button type="submit">Login</button>
</form>
```

### Controller

```csharp
[HttpPost]
public IActionResult Login(string username, string password)
{
    ViewBag.User = username;
    return View();
}
```

### Explanation

Form ផ្ញើ

```
username = admin
password = 123
```

MVC bind:

```
username → string username
password → string password
```

---

# 5.2 Complex Type Binding

**Complex Type Binding** គឺជាការផ្គូរផ្គង HTTP Request ទៅកាន់ **object (class)** មួយ។

មានន័យថា MVC នឹងយក Request Data ទៅបំពេញទៅក្នុង **Properties របស់ Model Class**។

---

## លក្ខខណ្ឌសម្រាប់ Complex Binding

1. **Property Name ត្រូវដូចនឹង Request Key**
2. **Property ត្រូវជា public**
3. **Property ត្រូវមាន getter និង setter (read-write)**

---

## Example 1: Model Class

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

---

## Example 2: View (Form)

```html
<form asp-action="Create" method="post">

    <input type="text" name="Id" placeholder="Student Id" />

    <input type="text" name="Name" placeholder="Student Name" />

    <input type="number" name="Age" placeholder="Student Age" />

    <button type="submit">Save</button>

</form>
```

---

## Example 3: Controller

```csharp
[HttpPost]
public IActionResult Create(Student student)
{
    return View(student);
}
```

---

## Model Binding Process

Form Data:

```
Id = 1
Name = Dara
Age = 20
```

MVC នឹងធ្វើការ bind ដូចខាងក្រោម

```
student.Id = 1
student.Name = "Dara"
student.Age = 20
```

ហើយបញ្ជូន object នេះទៅ Action Method។

---

# Example 4: Using Razor Tag Helper (Recommended)

### View

```html
<form asp-action="Create" method="post">

    <input asp-for="Id" class="form-control" />

    <input asp-for="Name" class="form-control" />

    <input asp-for="Age" class="form-control" />

    <button type="submit">Save</button>

</form>
```

### Controller

```csharp
[HttpPost]
public IActionResult Create(Student student)
{
    if (ModelState.IsValid)
    {
        // save to database
    }

    return View(student);
}
```

---

# Model Binding Sources Priority

ASP.NET MVC នឹងស្វែងរកតម្លៃតាមលំដាប់ដូចខាងក្រោម៖

1. **Form Data**
2. **Route Data**
3. **Query String**

---

# Summary

| Model Binding Type   | Description                                      |
| -------------------- | ------------------------------------------------ |
| Simple Type Binding  | Bind primitive data types (int, string, bool)    |
| Complex Type Binding | Bind HTTP Request ទៅ Model Class                 |
| Data Source          | Form Data, Route Data, Query String              |
| Important Rule       | Request key ត្រូវតែដូច Parameter ឬ Property Name |

---

បើអ្នកចង់ ខ្ញុំអាចបង្កើត **Diagram Flow Model Binding (User → MVC → Controller → Model)** ដែលល្អសម្រាប់ **ដាក់ក្នុង Slide បង្រៀន**។
