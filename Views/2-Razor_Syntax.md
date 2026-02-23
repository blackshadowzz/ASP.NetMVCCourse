## 5. Razor Syntax

**Razor** គឺជា **View Engine** មួយក្នុង **ASP.NET MVC** ដែលអនុញ្ញាតិឱ្យអ្នកសរសេរ **C# code** ជាមួយ **HTML** នៅក្នុង **View** តែមួយ។
Razor syntax មាន ២ ប្រភេទសំខាន់៖

1. **Razor Code Expressions**
2. **Razor Code Blocks**

---

### 5.1 Razor Code Expressions

- ចាប់ផ្តើមដោយ **`@`** បន្ទាប់ដោយ **C# expression**។
- ប្រើសម្រាប់ **output data** ចេញទៅ HTML.
- **មិនត្រូវប្រើ semi-colon (;)** នៅចុង expression.

**ឧទាហរណ៍៖**

```cshtml
@* Razor Expression Example *@
<h1>សួស្តី @Model.Name!</h1>

<p>ថ្ងៃនេះថ្ងៃទី: @DateTime.Now.ToString("dd/MM/yyyy")</p>

<p>ចំនួនទំនិញសរុប: @Model.TotalItems</p>
```

**អត្ថន័យ៖**

- `@Model.Name` → បង្ហាញឈ្មោះអ្នកប្រើពី model
- `@DateTime.Now.ToString("dd/MM/yyyy")` → បង្ហាញថ្ងៃសព្វថ្ងៃ
- `@Model.TotalItems` → បង្ហាញចំនួនទំនិញសរុប

---

### 5.2 Razor Code Blocks

- ចាប់ផ្តើមដោយ **`@`** បន្ទាប់ដោយ `{` ហើយបញ្ចប់ដោយ `}`
- ប្រើសម្រាប់ **multiple statements** ឬ **logic server-side**
- **រាល់ statement ត្រូវបញ្ចប់ដោយ semi-colon (;)**

**ឧទាហរណ៍៖**

```cshtml
@{
    // Razor Code Block Example
    var greeting = "សូមស្វាគមន៍";
    var hour = DateTime.Now.Hour;
    string message;

    if(hour < 12)
    {
        message = "ព្រឹកសួស្តី!";
    }
    else if(hour < 18)
    {
        message = "រសៀលសួស្តី!";
    }
    else
    {
        message = "ល្ងាចសួស្តី!";
    }
}

<h2>@greeting, @Model.Name!</h2>
<p>@message</p>
```

**អត្ថន័យ៖**

- កូដក្នុង `{ ... }` ជា **server-side C#**
- អាចប្រើ **loops**, **conditions**, **variables**
- **output** ត្រូវប្រើ `@variableName` នៅក្នុង HTML

---

### 🔹 Tip

- **Razor Expression**: សម្រាប់ **ការបង្ហាញ data តែមួយ**
- **Razor Block**: សម្រាប់ **logic/loop/condition multiple statements**

**Loop Example (Razor Code Block):**

```cshtml
@{
    var items = new List<string> { "Apple", "Banana", "Cherry" };
}

<ul>
@foreach(var item in items)
{
    <li>@item</li>
}
</ul>
```

**Output HTML:**

```html
<ul>
  <li>Apple</li>
  <li>Banana</li>
  <li>Cherry</li>
</ul>
```

---

## 5.3 `@using` Directive និង `_ViewImports`

### 1️⃣ `@using` Directive

- ប្រើសម្រាប់ **import namespace** មួយចូលទៅក្នុង **View** ដើម្បីអាចប្រើ **classes, models, enums, methods** នៅក្នុង namespace នោះ។
- ចាប់ផ្តើមដោយ **`@using NamespaceName`**

**ឧទាហរណ៍៖**

```cshtml id="f2k1dj"
@using WebApp.Models

<h1>សូមស្វាគមន៍ @Model.Name!</h1>
```

**អត្ថន័យ៖**

- `WebApp.Models` គឺជា namespace ដែលមាន **Model classes**
- អ្នកមិនចាំបាច់សរសេរ full namespace ពីពេលប្រើ `Model` ទៀត

---

### 2️⃣ `_ViewImports.cshtml`

- ជា **file special** សម្រាប់ import **namespace** និង **tag helpers** ទៅ **views ទាំងអស់** ក្នុង **application**
- ស្ថិតនៅក្នុង **Views folder**
- អាចដាក់ **`@using`**, **`@addTagHelper`**, និង **`@inject`** នៅទីនេះ

**ឧទាហរណ៍ `_ViewImports.cshtml`**:

```cshtml id="0vq7nb"
@using WebApp.Models
@using WebApp.Services
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

**អត្ថន័យ**:

- `@using WebApp.Models` → គ្រប់ view អាចប្រើ models ក្នុង namespace នេះ
- `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` → enable tag helpers globally

---

## 5.4 ការប្រកាសអថេរ (Variable Declaration)

- អ្នកអាចប្រកាសអថេរ **Razor code block** ដោយប្រើ **`var`** ឬ **C# data type**
- ត្រូវសរសេរ **semicolon (;)** នៅចុង statement

---

### ប្រើ `var`

```cshtml id="x8u2qo"
@{
    var name = "Sophea";
    var age = 25;
}

<p>ឈ្មោះ: @name</p>
<p>អាយុ: @age</p>
```

**អត្ថន័យ**:

- `var` ធ្វើអោយ C# ចេញ type automatically (string, int, etc.)
- អាចប្រើក្នុង **code blocks** និង **loops**

---

### ប្រើ C# Data Type

```cshtml id="s5k3lm"
@{
    string city = "Phnom Penh";
    int totalItems = 10;
    bool isActive = true;
}

<p>ទីក្រុង: @city</p>
<p>ចំនួនទំនិញ: @totalItems</p>
<p>ស្ថានភាព: @(isActive ? "Active" : "Inactive")</p>
```

**អត្ថន័យ**:

- ប្រកាសប្រភេទ data type ជាក់លាក់ (string, int, bool…)
- ងាយស្រួលសម្រាប់ **large projects** ដែលត្រូវប្រកាស type ជាក់លាក់

---

### 🔹 Tip

- ប្រើ `var` → ខ្លី, auto type inference
- ប្រើ explicit type → សំរាប់ readability និង type safety
- `_ViewImports.cshtml` → ជួយ **global import** មិនចាំបាច់សរសេរ `@using` ម្តងទៀតក្នុង view គ្រប់មួយ

---
