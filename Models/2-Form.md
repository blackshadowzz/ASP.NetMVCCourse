## 4. ការប្រមូលទិន្ន័យ (Form Data Collection)

**Form** ជាឃ្លាំងមួយដែលផ្ទុក **form controls** ច្រើន ដែលមានមុខងារផ្ទាល់ខ្លួន សម្រាប់ប្រមូលទិន្នន័យពីអ្នកប្រើ និងផ្ញើទៅកាន់ **server**។
Form controls ធម្មតារួមមាន៖

- **Text box** – សម្រាប់បញ្ចូលអក្សរ ឬលេខ
- **Password box** – សម្រាប់បញ្ចូលពាក្យសម្ងាត់
- **Email box** – សម្រាប់បញ្ចូលអ៊ីមែល
- **Checkbox / Radio button** – សម្រាប់ជម្រើសបញ្ចាក់/ជ្រើស
- **Dropdown (Select)** – សម្រាប់ជម្រើសច្រើនជាមួយ menu
- **Button / Submit** – សម្រាប់បញ្ជូនទិន្នន័យទៅ server

---

## 4.1 Simple Form Example

នេះគឺជា **ឧទាហរណ៍ Form មូលដ្ឋាន** ជាមួយ HTML និង Bootstrap (UI)៖

```html
<form action="/Home/SubmitForm" method="post">
  <div class="mb-3">
    <label for="name" class="form-label">Name</label>
    <input
      type="text"
      class="form-control"
      id="name"
      name="Name"
      placeholder="Enter your name"
      required
    />
  </div>

  <div class="mb-3">
    <label for="email" class="form-label">Email</label>
    <input
      type="email"
      class="form-control"
      id="email"
      name="Email"
      placeholder="Enter your email"
      required
    />
  </div>

  <div class="mb-3">
    <label for="password" class="form-label">Password</label>
    <input
      type="password"
      class="form-control"
      id="password"
      name="Password"
      placeholder="Enter your password"
      required
    />
  </div>

  <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

**អធិប្បាយ៖**

1. `action="/Home/SubmitForm"` → បញ្ជាក់ **Controller Action** ដែលទទួលទិន្នន័យ
2. `method="post"` → បញ្ជូនទិន្នន័យដោយ **POST request**
3. `name="..."` → ឈ្មោះ field ដែលសំរាប់ **binding ទៅ Model ឬ parameter** នៅ Controller
4. `required` → បញ្ជាក់ថាតម្លៃនេះគឺចាំបាច់

---

## 4.2 Tag Helpers

**Tag Helpers** នៅក្នុង **ASP.NET Core MVC / Razor Pages** ជា **feature** ដែលអនុញ្ញាតឲ្យ **server-side code** ចូលរួមក្នុងការបង្កើត និង **render HTML elements** ក្នុង Razor view។ វាផ្ដល់ភាពងាយស្រួល និង type-safe ក្រៅពីការសរសេរ HTML ធម្មតា។

Tag Helpers គឺមានប្រភេទចម្បងៗ៖

- **Form Tag Helper**
  ជួយបង្កើត `<form>` ដែល **bind ទៅ Controller Action** ឬ Razor Page handler, និងគ្រប់គ្រង **method** និង **anti-forgery token** ស្វ័យប្រវត្តិ។

- **Input Tag Helper**
  ជំនួស `<input>` ទូទៅ ដើម្បី bind ទៅ **Model property** និងបង្កើត attributes (like `id`, `name`, `value`) ដោយស្វ័យប្រវត្តិ។

- **Label Tag Helper**
  ជំនួស `<label>` ទូទៅ ដើម្បី bind ទៅ **Model property** ហើយបង្ហាញ label text ដែលសាកសម។

- **Anchor Tag Helper**
  ជំនួស `<a>` ដែលអាច bind ទៅ **Controller Action** ឬ Razor Page, បង្កើត **href** dynamically។

- **Other Tag Helpers**
  - Validation Tag Helpers: `asp-validation-for` → បង្ហាញ error messages
  - Select / TextArea Tag Helpers: `asp-for` → bind ទៅ Model

---

### 4.2.1 Example: Form with Tag Helpers

```csharp
@model WebApp.Models.UserModel

<form asp-action="SubmitForm" method="post">
    <div class="mb-3">
        <label asp-for="Name" class="form-label"></label>
        <input asp-for="Name" class="form-control" />
        <span asp-validation-for="Name" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Email" class="form-label"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Password" class="form-label"></label>
        <input asp-for="Password" type="password" class="form-control" />
        <span asp-validation-for="Password" class="text-danger"></span>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

**អធិប្បាយ៖**

1. `asp-for="PropertyName"` → bind input/label/validation ទៅ **Model property**
2. `asp-action="SubmitForm"` → bind form ទៅ Controller Action
3. Validation Tag Helper (`asp-validation-for`) បង្ហាញ **error messages** តាម **ModelState**

---

## 4.2.1 Form Tag Helper

**Form Tag Helper** អនុញ្ញាតឲ្យអ្នកបង្កើត `<form>` ដែល **bind ទៅ server-side code** (Controller Action / Razor Page handler) ដោយប្រើ **Tag Helper attributes**។ វាធ្វើឲ្យ form ជា **type-safe** និងងាយស្រួលក្នុងការគ្រប់គ្រង route និង method។

### Attributes ចម្បង

| Attribute                 | ការប្រើប្រាស់                                                       |
| ------------------------- | ------------------------------------------------------------------- |
| **asp-action**            | បញ្ជាក់ **Action method** នៅក្នុង Controller ដែល form នឹង submit ទៅ |
| **asp-controller**        | បញ្ជាក់ **Controller** ដែលមាន Action method                         |
| **asp-area**              | ប្រើសម្រាប់ **Area routing** ក្នុង ASP.NET Core MVC                 |
| **asp-route-{parameter}** | បញ្ជាក់ **route parameter** ដែលចង់ផ្ញើជាមួយ form                    |

### Example: Form Tag Helper

```csharp
@model WebApp.Models.UserModel

<form asp-action="SubmitForm" asp-controller="Home" method="post">
    <div class="mb-3">
        <label asp-for="Name" class="form-label"></label>
        <input asp-for="Name" class="form-control" />
        <span asp-validation-for="Name" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Email" class="form-label"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

**អធិប្បាយ៖**

- Form នេះ **submit ទៅ** `HomeController.SubmitForm()`
- `asp-action` និង `asp-controller` បង្កើត **action URL** ស្វ័យប្រវត្តិ
- `asp-for` ចូលរួម bind input ទៅ **Model property**
- Validation span ប្រើ `asp-validation-for` ដើម្បីបង្ហាញ errors

---

## 4.2.2 Anchor Tag Helper

**Anchor Tag Helper** អនុញ្ញាតឲ្យ `<a>` tag បង្កើត **hyperlink dynamically** ដោយប្រើ **server-side attributes**។ វាមានប្រយោជន៍សម្រាប់ generating **URLs** ដោយស្វ័យប្រវត្តិ, ស្រួល maintenance, និង type-safe។

### Attributes ចម្បង

| Attribute                 | ការប្រើប្រាស់                                       |
| ------------------------- | --------------------------------------------------- |
| **asp-action**            | បញ្ជាក់ **Action method** នៅ Controller ដែលតំណនឹងទៅ |
| **asp-controller**        | បញ្ជាក់ **Controller** ដែលមាន Action method         |
| **asp-area**              | ប្រើសម្រាប់ **Area routing**                        |
| **asp-route-{parameter}** | បញ្ជាក់ **route parameter** ដើម្បី append ជាមួយ URL |

### Example: Anchor Tag Helper

```html
<a
  asp-action="Details"
  asp-controller="Products"
  asp-route-id="5"
  class="btn btn-info"
>
  View Product
</a>
```

**អធិប្បាយ៖**

- Anchor នេះ generate **URL** ដូចជា `/Products/Details/5`
- `asp-route-id="5"` បញ្ចូល **route parameter** id=5
- អាចប្រើ CSS classes និង attributes ផ្សេងៗដូចជា `<a>` ធម្មតា

---

### Form vs Anchor Tag Helper

| Feature          | Form Tag Helper                                             | Anchor Tag Helper                                           |
| ---------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| Element          | `<form>`                                                    | `<a>`                                                       |
| Purpose          | Submit data to server                                       | Navigate to server action / page                            |
| Key Attributes   | asp-action, asp-controller, asp-area, asp-route-{parameter} | asp-action, asp-controller, asp-area, asp-route-{parameter} |
| Server-Side Bind | Model binding via inputs                                    | Route parameters via href generation                        |
| Example          | Submit User Form                                            | Link to Product Details                                     |

---

## 1. Input Type Attributes (HTML / Tag Helper)

These are **HTML input types** that define the kind of data a user can enter. Tag Helpers like `asp-for` automatically set the appropriate `type` based on the model or explicit type.

| Input Type     | Description                                |
| -------------- | ------------------------------------------ |
| text           | Standard single-line text input            |
| password       | Input masked for passwords                 |
| email          | Input validated for email format           |
| number         | Input only allows numeric values           |
| tel            | Input for phone numbers                    |
| url            | Input validated as URL                     |
| search         | Input for search queries                   |
| date           | Input for date selection                   |
| datetime-local | Input for date and time (without timezone) |
| month          | Input for month/year selection             |
| week           | Input for week/year selection              |
| time           | Input for time only                        |
| checkbox       | Boolean input, checked or unchecked        |
| radio          | Choice from a set of options               |
| file           | Upload file input                          |
| hidden         | Hidden input, not visible to user          |
| range          | Input slider with min/max                  |
| color          | Input color picker                         |

**Example in Razor with Tag Helper:**

```csharp
<input asp-for="Email" type="email" class="form-control" />
<input asp-for="Age" type="number" class="form-control" />
```

---

## 2. Data Annotation Attributes (Server-Side Validation / Metadata)

These are **C# attributes** applied to **Model properties** to enforce **validation, display, and behavior**. ASP.NET Core MVC reads these for **server-side and client-side validation**.

| Attribute                              | Description                                                 |
| -------------------------------------- | ----------------------------------------------------------- |
| [Required]                             | Property must have a value; cannot be null/empty            |
| [StringLength(max, MinimumLength=min)] | Limits string length, can specify min/max                   |
| [MaxLength(n)]                         | Maximum string or array length                              |
| [MinLength(n)]                         | Minimum string or array length                              |
| [Range(min, max)]                      | Numeric range validation                                    |
| [EmailAddress]                         | Validates property as email format                          |
| [Phone]                                | Validates as a phone number                                 |
| [Url]                                  | Validates as a URL                                          |
| [CreditCard]                           | Validates credit card format                                |
| [Compare("OtherProperty")]             | Ensures two fields match (e.g., password confirmation)      |
| [RegularExpression("pattern")]         | Regex pattern validation                                    |
| [DataType(DataType.*)]                 | Specifies type for rendering (email, date, phone, password) |
| [Display(Name = "…")]                  | Sets display name in labels / validation messages           |
| [ScaffoldColumn(false)]                | Prevents property from being scaffolded in forms            |
| [BindNever]                            | Excludes property from model binding                        |

**Example in Model:**

```csharp
public class UserModel
{
    [Required]
    [StringLength(50)]
    public string Name { get; set; }

    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Compare("Password")]
    [DataType(DataType.Password)]
    public string ConfirmPassword { get; set; }
}
```

---

## 3. .NET Data Types for Form Fields

These are **C# property types** commonly used in models for form input. ASP.NET automatically maps these to HTML input types via Tag Helpers.

| C# Type                               | Typical HTML Input Type                 | Description                   |
| ------------------------------------- | --------------------------------------- | ----------------------------- |
| string                                | text, email, password, tel, url         | Single-line text input        |
| int / long / double / float / decimal | number                                  | Numeric input                 |
| bool                                  | checkbox                                | True/false input              |
| DateTime                              | date, datetime-local, time, month, week | Date or time input            |
| DateTimeOffset                        | datetime-local                          | Date and time with offset     |
| Guid                                  | hidden / text                           | Globally unique identifier    |
| Enum                                  | select                                  | Drop-down list of enum values |
| IFormFile                             | file                                    | File upload                   |
| IList<T> / IEnumerable<T>             | multiple checkboxes / select multiple   | Multiple selection            |

**Example in Razor:**

```csharp
<input asp-for="Age" type="number" class="form-control" />
<input asp-for="BirthDate" type="date" class="form-control" />
<input asp-for="IsActive" type="checkbox" class="form-check-input" />
```

---

### Summary

- **Input Type Attributes** → control HTML input type & browser validation
- **Data Annotation Attributes** → define server-side validation & metadata
- **.NET Data Types** → define the data model and influence input rendering

---

## 4.2.4 Select Tag Helper

**Select Tag Helper** អនុញ្ញាតឲ្យបង្កើត `<select>` dropdown ដែល **bind ទៅ Model property** និង dynamically generate **options** ពី server-side data. វាមានប្រយោជន៍សម្រាប់ជម្រើសច្រើនជាមួយ **Tag Helper attributes**។

### Attributes ចម្បង

| Attribute     | Description                                                                           |
| ------------- | ------------------------------------------------------------------------------------- |
| **asp-for**   | Bind dropdown value to **Model property**                                             |
| **asp-items** | Provide a list of items (SelectList, IEnumerable<SelectListItem>) to populate options |

---

### Example: Select Tag Helper

**Model:**

```csharp
public class UserModel
{
    public int SelectedSubjectId { get; set; }
    public List<SelectListItem> Subjects { get; set; }
}
```

**Controller:**

```csharp
public IActionResult Create()
{
    var model = new UserModel
    {
        Subjects = new List<SelectListItem>
        {
            new SelectListItem { Value = "1", Text = "Math" },
            new SelectListItem { Value = "2", Text = "Physics" },
            new SelectListItem { Value = "3", Text = "Chemistry" }
        }
    };
    return View(model);
}
```

**View (Razor):**

```csharp id="h3jnk9"
@model UserModel

<form asp-action="Create" method="post">
    <div class="mb-3">
        <label asp-for="SelectedSubjectId" class="form-label"></label>
        <select asp-for="SelectedSubjectId" asp-items="Model.Subjects" class="form-select">
            <option value="">-- Select Subject --</option>
        </select>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

**អធិប្បាយ៖**

- `asp-for="SelectedSubjectId"` → bind selected value to Model property
- `asp-items="Model.Subjects"` → generate `<option>` elements automatically
- អាចបន្ថែម default `<option>` ដោយសរសេរផ្ទាល់ក្នុង HTML

---

## 4.3.5 TextArea Tag Helper

**TextArea Tag Helper** ជំនួស `<textarea>` HTML ដោយ bind ទៅ **Model property** និង support **server-side validation**។ វាសម្រាប់បញ្ចូល **multi-line text** (comments, descriptions, etc.)

### Attributes ចម្បង

| Attribute                     | Description                                   |
| ----------------------------- | --------------------------------------------- |
| **asp-for**                   | Bind textarea value to **Model property**     |
| **rows / cols / placeholder** | HTML attributes for size and placeholder text |

---

### Example: TextArea Tag Helper

**Model:**

```csharp
public class CommentModel
{
    [Required]
    public string Message { get; set; }
}
```

**View (Razor):**

```csharp id="e3kdla"
@model CommentModel

<form asp-action="SubmitComment" method="post">
    <div class="mb-3">
        <label asp-for="Message" class="form-label"></label>
        <textarea asp-for="Message" class="form-control" rows="5" placeholder="Write your comment here..."></textarea>
        <span asp-validation-for="Message" class="text-danger"></span>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

**អធិប្បាយ៖**

- `asp-for="Message"` → bind textarea content to Model property
- Validation errors automatically displayed via `asp-validation-for`

---
