# 8. Page Layout

ក្នុងការកសាង website ជាទូទៅ, website ត្រូវមាន **menu, header, footer និង sidebar** ជាធាតុមូលដ្ឋាន។
**Layout page** ជួយកំណត់ UI មូលដ្ឋានទាំងនេះមួយកន្លែង, ហើយអាចប្រើបានទាំងមូលនៅក្នុងគេហទំព័រ។

**Layout page** ត្រូវស្ថិតនៅក្នុង `Views/Shared` directory និងឈ្មោះចាប់ផ្តើមដោយ `_` (underscore) ដូចជា `_Layout.cshtml`។

---

## 8.1 បង្កើត Layout

1. បង្កើត folder **Shared** ក្នុង `Views` (បើមិនមាន)
2. Right-click លើ `Shared` → **Add → New Item → Razor Layout** → **Add**
3. ឧទាហរណ៍ឈ្មោះ file: `_Layout.cshtml`

## Example 1

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>@ViewData["Title"] - MyWebsite</title>
    <link rel="stylesheet" href="~/css/bootstrap.min.css" />
  </head>
  <body>
    <header>
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="#">MyWebsite</a>
        <ul class="navbar-nav">
          <li class="nav-item"><a class="nav-link" href="/">Home</a></li>
          <li class="nav-item"><a class="nav-link" href="/About">About</a></li>
        </ul>
      </nav>
    </header>

    <div class="container-fluid">
      <div class="row">
        <aside class="col-2 bg-light p-3">
          <!-- Sidebar -->
          <ul>
            <li><a href="/Products">Products</a></li>
            <li><a href="/Contact">Contact</a></li>
          </ul>
        </aside>

        <main class="col-10 p-3">
          @RenderBody()
          <!-- Content នៃ page នីមួយៗនឹងបញ្ចូលនៅទីនេះ -->
        </main>
      </div>
    </div>

    <footer class="bg-dark text-white text-center p-3">
      &copy; 2026 MyWebsite
    </footer>
  </body>
</html>
```

**ពិពណ៌នា**៖

- `@RenderBody()` → ជា placeholder សម្រាប់ content នៃ page ផ្ទាល់ខ្លួន
- `header, footer, sidebar` → ត្រូវបានកំណត់ក្នុង layout មួយនេះ, មិនចាំបាច់សរសេរ នៅក្នុង page ទាំងអស់ឡើយ

---

## 8.2 ប្រើប្រាស់ Layout

### កំណត់ក្នុង **page ផ្ទាល់ខ្លួន**

នៅលើ Razor page ឬ MVC view, អ្នកអាចកំណត់ Layout ដោយប្រើ property `Layout`៖

```csharp
@{
    Layout = "~/Views/Shared/_Layout.cshtml";
    ViewData["Title"] = "Home Page";
}

<h1>Welcome to My Website</h1>
<p>This is the home page content.</p>
```

### ចែករំលែកទៅគ្រប់ page

ប្រសិនបើចង់ប្រើ layout ទៅគ្រប់ page, បង្កើត `_ViewStart.cshtml` ក្នុង `Views` folder:

```csharp
@{
    Layout = "_Layout";
}
```

- ពេលនេះ **គ្រប់ page** នៅក្នុង folder នោះនឹងប្រើ layout ដោយស្វ័យប្រវត្តិ
- អ្នកអាច override នៅ page ផ្ទាល់ខ្លួនបាន ប្រសិនបើចង់មាន layout ផ្សេង

---

### 8.3 ចំណាំ

- `_Layout.cshtml` ត្រូវស្ថិតនៅ `Views/Shared` ឬ folder ផ្សេងដែល Views រួមអាច access បាន
- ឈ្មោះចាប់ដោយ `_` → signal ជា partial / shared resource
- គ្រប់ page មិនចាំបាច់ include header/footer/sidebar ទៀត

---

💡 **Tip**: ប្រសិនបើអ្នកមាន **multiple layouts** (e.g., one for admin, one for public), អាចបង្កើត `_AdminLayout.cshtml` និង override `Layout` នៅ page admin ផ្ទាល់ខ្លួន។

---

## Example 2 **\_Layout.cshtml (Professional Bootstrap Layout)**

```html
@* File: Views/Shared/_Layout.cshtml *@
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>@ViewData["Title"] - MyWebsite</title>

    <!-- Bootstrap CSS -->
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"
      rel="stylesheet"
    />
    <link rel="stylesheet" href="~/css/site.css" />
    @* Optional custom styles *@
  </head>
  <body>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
      <div class="container-fluid">
        <a class="navbar-brand" href="/">MyWebsite</a>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item">
              <a class="nav-link active" href="/">Home</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/About">About</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/Products">Products</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/Contact">Contact</a>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Main layout: Sidebar + Content -->
    <div class="container-fluid">
      <div class="row">
        <!-- Sidebar -->
        <nav
          id="sidebarMenu"
          class="col-md-3 col-lg-2 d-md-block bg-light sidebar collapse"
        >
          <div class="position-sticky pt-3">
            <ul class="nav flex-column">
              <li class="nav-item">
                <a class="nav-link active" aria-current="page" href="/">
                  Dashboard
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="/Orders"> Orders </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="/Customers"> Customers </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="/Reports"> Reports </a>
              </li>
            </ul>
          </div>
        </nav>

        <!-- Content area -->
        <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4 py-4">
          <h1 class="h2">@ViewData["Title"]</h1>
          <hr />
          @RenderBody()
        </main>
      </div>
    </div>

    <!-- Footer -->
    <footer class="footer mt-auto py-3 bg-dark text-white text-center">
      <div class="container">
        <span>&copy; 2026 MyWebsite. All rights reserved.</span>
      </div>
    </footer>

    <!-- Bootstrap JS Bundle -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
  </body>
</html>
```

---

### **ប្រើប្រាស់ Layout ក្នុង Page**

**Views/Home/Index.cshtml**

```csharp
@{
    ViewData["Title"] = "Dashboard";
}

<p>Welcome to MyWebsite! This is the main dashboard content.</p>
```

- Layout ត្រូវបានកំណត់ដោយ `_ViewStart.cshtml`:

```csharp
@{
    Layout = "_Layout";
}
```

---

# **8.3 @RenderBody() និង @RenderSection()**

### **1️⃣ @RenderBody()**

- ជា **placeholder** មួយនៅក្នុង **Layout Page** ដែលបង្ហាញ **content នៃ child view** ដែលប្រើ layout នោះ។
- ក្នុង **Layout Page**, អាចមានតែមួយ `@RenderBody()` ប៉ុណ្ណោះ។

**ឧទាហរណ៍**:

**\_Layout.cshtml**

```html id="rdb-example"
<body>
  <header>
    <h1>My Website Header</h1>
  </header>

  <main>
    @RenderBody()​​​
    <!-- Child content នឹងបង្ហាញនៅក្នុងទីនេះ-->
  </main>

  <footer>&copy; 2026 MyWebsite</footer>
</body>
```

**Views/Home/Index.cshtml**

```csharp id="child-body"
@{
    ViewData["Title"] = "Home Page";
}

<h2>Welcome to Home Page</h2>
<p>This content will appear in the @RenderBody() location.</p>
```

**លទ្ធផល**:

- `<h2>Welcome to Home Page</h2>` និង `<p>...</p>` នឹងបញ្ចូលក្នុង `<main>` ដែលមាន `@RenderBody()` នៅ layout page។

---

### **2️⃣ @RenderSection()**

- ជា **placeholder សម្រាប់ section ជាក់លាក់** ដែលអាចកំណត់នៅ **child view**។
- អាចមាន **ច្រើន** នៅក្នុង layout page (unlike `@RenderBody()` ដែលមានតែមួយ)
- Syntax៖

```csharp
@RenderSection("SectionName", required: true|false)
```

- `required: true` → ត្រូវតែមាន section នៅ child view
- `required: false` → child view មាន section ក៏បាន អត់ក៏បាន

---

**ឧទាហរណ៍ 1: Optional Section**

**\_Layout.cshtml**

```html id="rendersection-optional"
<head>
  <title>@ViewData["Title"]</title>
  @RenderSection("Styles", required: false)
  <!-- optional CSS section -->
</head>
<body>
  @RenderBody() @RenderSection("Scripts", required: false)
  <!-- optional JS section -->
</body>
```

**Views/Home/Index.cshtml**

```csharp
@{
    ViewData["Title"] = "Home Page";
}

<h2>Home Page Content</h2>

@section Scripts {
    <script>
        console.log("Home page script loaded.");
    </script>
}
```

**លទ្ធផល**:

- `@RenderBody()` បង្ហាញ `<h2>Home Page Content</h2>`
- `@RenderSection("Scripts")` បង្ហាញ script នៅទីតាំងដែលកំណត់ក្នុង layout

---

**ឧទាហរណ៍ 2: Required Section**

**\_Layout.cshtml**

```csharp
@RenderSection("HeaderContent", required: true)
```

**Views/Home/Index.cshtml**

```csharp
@section HeaderContent {
    <div class="page-header">
        <h1>Welcome to My Website</h1>
    </div>
}
```

- បើ child view មិនមាន `@section HeaderContent` → build error

---

### **📌 Key Points**

1. `@RenderBody()`
   - តែមួយក្នុង layout
   - ចេញ content សំខាន់របស់ child view

2. `@RenderSection("Name")`
   - ច្រើនបាន
   - បង្ហាញ content optional ឬ required
   - ល្អសម្រាប់ **styles, scripts, extra content areas**

---

💡 **Tip**:

- ប្រើ `@RenderSection("Scripts", required: false)` ដើម្បីបន្ថែម JS ផ្ទាល់ខ្លួននៅ page មិនប៉ះ layout ផ្ទាល់។
- ប្រើ `@RenderSection` សម្រាប់ **dynamic page elements** មិនចង់ទូទាំង page ទៅ header/footer។

---
