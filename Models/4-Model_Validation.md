# 6. Model Validation

**Model Validation** គឺជាយន្តការសម្រាប់ **ពិនិត្យភាពត្រឹមត្រូវ (Validity)** នៃទិន្នន័យដែល **User បញ្ជូនមកកាន់ Server** តាមរយៈ HTTP Request មុនពេលធ្វើការប្រើប្រាស់ ឬរក្សាទុកក្នុង Database។

គោលបំណងសំខាន់ៗ៖

* ការពារការបញ្ចូលទិន្នន័យមិនត្រឹមត្រូវ
* កាត់បន្ថយ Error នៅក្នុង Application
* ការពារបញ្ហាសុវត្ថិភាព (Security)
* ធានាថាទិន្នន័យដែល Bind ទៅ Model មានភាពត្រឹមត្រូវ

នៅក្នុង **ASP.NET Core MVC** មានវិធីសាស្ត្រ Validation ចម្បង **៣ ប្រភេទ**៖

1. **Server-Side Validation**
2. **Client-Side Validation**
3. **Remote Validation**

---

# 6.1 Server-Side Validation

**Server-Side Validation** គឺជាការពិនិត្យទិន្នន័យនៅលើ **Server** បន្ទាប់ពី User Submit Form មកកាន់ Controller។

Server នឹងពិនិត្យថា Data ត្រឹមត្រូវឬអត់ មុននឹងដំណើរការបន្ត។

មានវិធីសាស្ត្រ ២៖

1. **Manual Model Validation**
2. **Data Annotation Validation**

---

# 6.1.1 Manual Validation

Manual Validation គឺជាការពិនិត្យទិន្នន័យដោយ **Developer សរសេរកូដដោយផ្ទាល់** ក្នុង Controller។

### Method ដែលប្រើញឹកញាប់

### 1. ModelState.AddModelError()

ប្រើសម្រាប់ **បន្ថែម Error Message ទៅ ModelState Object**

```csharp
ModelState.AddModelError(string key, string errorMessage);
```

**Parameters**

| Parameter    | Description            |
| ------------ | ---------------------- |
| key          | ឈ្មោះ property         |
| errorMessage | សារ error ដែលចង់បង្ហាញ |

---

### 2. ModelState.IsValid

ប្រើសម្រាប់ពិនិត្យថា **Model មាន Error ឬអត់**

Return Value

* **true** → Data ត្រឹមត្រូវ
* **false** → មាន Validation Error

---

## Example: Manual Validation

### Model

```csharp
public class Student
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

---

### Controller

```csharp
public class StudentController : Controller
{
    [HttpPost]
    public IActionResult Create(Student model)
    {
        if (string.IsNullOrEmpty(model.Name))
        {
            ModelState.AddModelError("Name", "Name is required");
        }

        if (model.Age < 18)
        {
            ModelState.AddModelError("Age", "Age must be at least 18");
        }

        if (ModelState.IsValid)
        {
            // Save data to database
            return RedirectToAction("Index");
        }

        return View(model);
    }
}
```

---

### View (Razor)

```html
<form asp-action="Create" method="post">

    <div>
        <label>Name</label>
        <input asp-for="Name" />
        <span asp-validation-for="Name"></span>
    </div>

    <div>
        <label>Age</label>
        <input asp-for="Age" />
        <span asp-validation-for="Age"></span>
    </div>

    <button type="submit">Submit</button>

</form>
```

---

### Output

បើ User បញ្ចូល៖

```
Name = ""
Age = 15
```

Result

```
Name is required
Age must be at least 18
```

---

# 6.1.2 Data Annotation Validation

**Data Annotation Validation** គឺជាការប្រើ **Attribute** នៅក្នុង Model Class ដើម្បីកំណត់ Rule សម្រាប់ Validation។

ASP.NET MVC នឹង **validate automatically** នៅពេល Model Binding។

---

## Example Model

```csharp
using System.ComponentModel.DataAnnotations;

public class Student
{
    [Required(ErrorMessage = "Name is required")]
    public string Name { get; set; }

    [Range(18, 60, ErrorMessage = "Age must be between 18 and 60")]
    public int Age { get; set; }

    [EmailAddress(ErrorMessage = "Invalid Email")]
    public string Email { get; set; }
}
```

---

## Controller

```csharp
[HttpPost]
public IActionResult Create(Student model)
{
    if (ModelState.IsValid)
    {
        return RedirectToAction("Index");
    }

    return View(model);
}
```

---

## View

```html
<form asp-action="Create" method="post">

    <div>
        <label>Name</label>
        <input asp-for="Name" class="form-control" />
        <span asp-validation-for="Name" class="text-danger"></span>
    </div>

    <div>
        <label>Age</label>
        <input asp-for="Age" class="form-control" />
        <span asp-validation-for="Age" class="text-danger"></span>
    </div>

    <div>
        <label>Email</label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>

    <button type="submit">Submit</button>

</form>
```

---

# Common Data Annotation Attributes

| Attribute    | Description                                 |
| ------------ | ------------------------------------------- |
| Required     | Field មិនអាចទទេ                             |
| StringLength | កំណត់ប្រវែងអក្សរ                            |
| Range        | កំណត់ចន្លោះលេខ                              |
| EmailAddress | Validate Email                              |
| Phone        | Validate Phone                              |
| Url          | Validate Website URL                        |
| Compare      | Compare fields (Password & ConfirmPassword) |

---

## Example: Compare Password

```csharp
public class Register
{
    [Required]
    public string Password { get; set; }

    [Compare("Password", ErrorMessage="Password not match")]
    public string ConfirmPassword { get; set; }
}
```

---

# 6.2 Client-Side Validation

**Client-Side Validation** គឺជាការពិនិត្យ Data នៅលើ **Browser (JavaScript)** មុននឹង Submit Form ទៅ Server។

អត្ថប្រយោជន៍៖

* Response លឿន
* កាត់បន្ថយ Server Load
* Improve User Experience

ASP.NET MVC ប្រើ

* **jQuery Validation**
* **jQuery Unobtrusive Validation**

---

### Required Scripts

```html
<script src="~/lib/jquery/jquery.min.js"></script>
<script src="~/lib/jquery-validation/jquery.validate.min.js"></script>
<script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
```

បន្ទាប់មក Validation នឹងដំណើរការ **Automatically** ពេលប្រើ Data Annotation។

---

# 6.3 Remote Validation

**Remote Validation** ប្រើសម្រាប់ពិនិត្យ Data តាមរយៈ **Ajax Call ទៅ Server**។

ប្រើនៅពេល

* Check Email Already Exists
* Check Username Already Taken

---

## Example

### Model

```csharp
public class Register
{
    [Required]
    [Remote(action: "IsEmailAvailable", controller: "Account")]
    public string Email { get; set; }
}
```

---

### Controller

```csharp
public class AccountController : Controller
{
    public IActionResult IsEmailAvailable(string email)
    {
        if (email == "admin@gmail.com")
        {
            return Json("Email already exists");
        }

        return Json(true);
    }
}
```

---

### Result

បើ User វាយ

```
admin@gmail.com
```

Browser នឹងបង្ហាញ

```
Email already exists
```

ដោយមិនចាំបាច់ Submit Form។

---

# Summary

| Validation Type        | Description                                  |
| ---------------------- | -------------------------------------------- |
| Manual Validation      | Developer សរសេរកូដ validate ក្នុង Controller |
| Data Annotation        | Validate តាម Attribute នៅ Model              |
| Client-side Validation | Validate នៅ Browser (JavaScript)             |
| Remote Validation      | Validate តាម Ajax Request ទៅ Server          |

---


# 6.2 ការបង្ហាញ Error Message នៅលើ View

នៅក្នុង **ASP.NET Core MVC**, បន្ទាប់ពី **Model Validation** ត្រូវបានពិនិត្យនៅ Server ហើយមាន Error កើតឡើង សារ Error នឹងត្រូវ **ផ្ទុកនៅក្នុង `ModelState`**។

នៅក្នុង **View (Razor)** យើងអាចបង្ហាញ Error Message ទាំងនេះដោយប្រើ **Validation Tag Helpers**។

Tag Helper សំខាន់ៗមាន៖

1. **asp-validation-summary**
2. **asp-validation-for**

---

# 6.2.1 asp-validation-summary

`asp-validation-summary` ប្រើសម្រាប់ **បង្ហាញ Error Messages ជាបញ្ជី (Summary List)** នៅផ្នែកខាងលើ Form។

Syntax

```html
<div asp-validation-summary="All"></div>
```

ASP.NET MVC នឹងយក Error ទាំងអស់ពី **ModelState** មកបង្ហាញក្នុង `<div>` នេះ។

---

## Options របស់ asp-validation-summary

| Value     | Description                             |
| --------- | --------------------------------------- |
| All       | បង្ហាញ Error ទាំងអស់ (Model + Property) |
| ModelOnly | បង្ហាញតែ Model-level Errors             |
| None      | មិនបង្ហាញ Summary                       |

---

## Example: asp-validation-summary="All"

### Model

```csharp
using System.ComponentModel.DataAnnotations;

public class Register
{
    [Required(ErrorMessage = "Name is required")]
    public string Name { get; set; }

    [Required(ErrorMessage = "Email is required")]
    [EmailAddress(ErrorMessage = "Invalid Email")]
    public string Email { get; set; }

    [Range(18,60,ErrorMessage="Age must be between 18 and 60")]
    public int Age { get; set; }
}
```

---

### Controller

```csharp
[HttpPost]
public IActionResult Register(Register model)
{
    if (!ModelState.IsValid)
    {
        return View(model);
    }

    return RedirectToAction("Success");
}
```

---

### View

```html
<form asp-action="Register" method="post">

<div asp-validation-summary="All" class="text-danger"></div>

<div>
    <label>Name</label>
    <input asp-for="Name" />
</div>

<div>
    <label>Email</label>
    <input asp-for="Email" />
</div>

<div>
    <label>Age</label>
    <input asp-for="Age" />
</div>

<button type="submit">Submit</button>

</form>
```

---

### Result

បើ User Submit Form ដោយទទេ

Browser នឹងបង្ហាញ

```
Name is required
Email is required
Age must be between 18 and 60
```

នៅលើផ្នែកខាងលើ Form។

---

# asp-validation-summary="ModelOnly"

ប្រើសម្រាប់បង្ហាញ **Model-level Error** ប៉ុណ្ណោះ (មិនបង្ហាញ Property Error)។

### Example

Controller

```csharp
[HttpPost]
public IActionResult Register(Register model)
{
    if(model.Email == "admin@gmail.com")
    {
        ModelState.AddModelError("", "Email already exists");
    }

    if(!ModelState.IsValid)
        return View(model);

    return RedirectToAction("Success");
}
```

View

```html
<div asp-validation-summary="ModelOnly" class="text-danger"></div>
```

Result

```
Email already exists
```

---

# 6.2.2 asp-validation-for

`asp-validation-for` ប្រើសម្រាប់ **បង្ហាញ Error Message របស់ Property មួយៗ**។

Syntax

```html
<span asp-validation-for="PropertyName"></span>
```

វានឹងបង្ហាញ Error ដែលកើតឡើងនៅ Property នោះ។

---

## Example

### Model

```csharp
public class Student
{
    [Required(ErrorMessage="Name is required")]
    public string Name { get; set; }

    [Range(18,60,ErrorMessage="Age must be between 18 and 60")]
    public int Age { get; set; }
}
```

---

### View

```html
<form asp-action="Create" method="post">

<div>
    <label>Name</label>
    <input asp-for="Name" class="form-control" />
    <span asp-validation-for="Name" class="text-danger"></span>
</div>

<div>
    <label>Age</label>
    <input asp-for="Age" class="form-control" />
    <span asp-validation-for="Age" class="text-danger"></span>
</div>

<button type="submit">Save</button>

</form>
```

---

### Result

បើ User បញ្ចូល

```
Name = ""
Age = 15
```

Browser នឹងបង្ហាញ

```
Name is required
Age must be between 18 and 60
```

នៅក្រោម Input Field នីមួយៗ។

---

# Example: Complete Form Validation

```html
<form asp-action="Create" method="post">

<div asp-validation-summary="All" class="text-danger"></div>

<div class="mb-3">
    <label>Name</label>
    <input asp-for="Name" class="form-control"/>
    <span asp-validation-for="Name" class="text-danger"></span>
</div>

<div class="mb-3">
    <label>Email</label>
    <input asp-for="Email" class="form-control"/>
    <span asp-validation-for="Email" class="text-danger"></span>
</div>

<div class="mb-3">
    <label>Age</label>
    <input asp-for="Age" class="form-control"/>
    <span asp-validation-for="Age" class="text-danger"></span>
</div>

<button class="btn btn-primary">Submit</button>

</form>
```

---

# Summary

| Tag Helper                         | Purpose                        |
| ---------------------------------- | ------------------------------ |
| asp-validation-summary             | បង្ហាញ Error Messages ជាបញ្ជី  |
| asp-validation-summary="All"       | បង្ហាញ Error ទាំងអស់           |
| asp-validation-summary="ModelOnly" | បង្ហាញ Model Errors            |
| asp-validation-summary="None"      | មិនបង្ហាញ Error                |
| asp-validation-for                 | បង្ហាញ Error របស់ Property មួយ |

---

