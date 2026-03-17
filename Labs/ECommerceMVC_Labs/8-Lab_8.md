# Lab Week 6

The task requires creating a `CustomersController` with four action methods (**Index**, **Create**, **Edit**, **Details**) and their corresponding Razor views in the `Views/Customers` folder.

### Step 1: Create the Customer Model (if not already done)

In the **Models** folder, add a new C# class named `Customer.cs`:

```csharp
using System.ComponentModel.DataAnnotations;

namespace YourProjectName.Models
{
    public class Customer
    {
        public int Id { get; set; }

        [Required]
        [StringLength(100)]
        [Display(Name = "Full Name")]
        public string FullName { get; set; }

        [Required]
        [EmailAddress]
        public string Email { get; set; }

        [Phone]
        [Display(Name = "Phone Number")]
        public string Phone { get; set; }

        [StringLength(200)]
        public string Address { get; set; }

        [Display(Name = "Registration Date")]
        [DataType(DataType.Date)]
        public DateTime RegistrationDate { get; set; } = DateTime.Now;
    }
}
```

This is a simple model for customers — adjust properties as needed for your E-Commerce project.

### Step 2: Create CustomersController Manually

Right-click the **Controllers** folder → **Add** → **Controller** → choose **MVC Controller - Empty** → name it `CustomersController.cs`.

Replace the content with this basic structure (we'll add logic later):

```csharp
using Microsoft.AspNetCore.Mvc;
using YourProjectName.Models;           // adjust namespace
using System.Collections.Generic;
using System.Linq;

namespace YourProjectName.Controllers
{
    public class CustomersController : Controller
    {
        // Temporary in-memory list (replace with DB later)
        private static List<Customer> _customers = new List<Customer>
        {
            new Customer { Id = 1, FullName = "Sokha Meas", Email = "sokha@example.com", Phone = "012345678" },
            new Customer { Id = 2, FullName = "Vannak Chheang", Email = "vannak@example.com" }
        };

        // GET: Customers (Index - list all)
        public IActionResult Index()
        {
            return View(_customers);
        }

        // GET: Customers/Create
        public IActionResult Create()
        {
            return View();
        }

        // POST: Customers/Create
        [HttpPost]
        [ValidateAntiForgeryToken]
        public IActionResult Create(Customer customer)
        {
            if (ModelState.IsValid)
            {
                customer.Id = _customers.Any() ? _customers.Max(c => c.Id) + 1 : 1;
                _customers.Add(customer);
                return RedirectToAction(nameof(Index));
            }
            return View(customer);
        }

        // GET: Customers/Edit/5
        public IActionResult Edit(int? id)
        {
            if (id == null) return NotFound();

            var customer = _customers.FirstOrDefault(c => c.Id == id);
            if (customer == null) return NotFound();

            return View(customer);
        }

        // POST: Customers/Edit/5
        [HttpPost]
        [ValidateAntiForgeryToken]
        public IActionResult Edit(int id, Customer customer)
        {
            if (id != customer.Id) return NotFound();

            if (ModelState.IsValid)
            {
                var existing = _customers.FirstOrDefault(c => c.Id == id);
                if (existing == null) return NotFound();

                existing.FullName = customer.FullName;
                existing.Email = customer.Email;
                existing.Phone = customer.Phone;
                existing.Address = customer.Address;
                // RegistrationDate usually not changed

                return RedirectToAction(nameof(Index));
            }
            return View(customer);
        }

        // GET: Customers/Details/5
        public IActionResult Details(int? id)
        {
            if (id == null) return NotFound();

            var customer = _customers.FirstOrDefault(c => c.Id == id);
            if (customer == null) return NotFound();

            return View(customer);
        }
    }
}
```

**Note**: This uses a static list for demo purposes. In later weeks (when you reach EF Core), replace it with DbContext injection and real database operations.

---

### Step 3: Create the 4 Views (in Views/Customers folder)

Right-click **Views** folder → **Add** → **New Folder** → name it `Customers`.

Then create each view (Right-click the new Customers folder → **Add** → **Razor View**):

1. **Index.cshtml** (list view)

```cshtml
@model IEnumerable<YourProjectName.Models.Customer>

@{
    ViewData["Title"] = "Customers List";
}

<h1>Customers</h1>

<p>
    <a asp-action="Create" class="btn btn-primary">Add New Customer</a>
</p>

<table class="table table-bordered table-hover">
    <thead class="table-dark">
        <tr>
            <th>@Html.DisplayNameFor(model => model.FullName)</th>
            <th>@Html.DisplayNameFor(model => model.Email)</th>
            <th>@Html.DisplayNameFor(model => model.Phone)</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@Html.DisplayFor(modelItem => item.FullName)</td>
                <td>@Html.DisplayFor(modelItem => item.Email)</td>
                <td>@Html.DisplayFor(modelItem => item.Phone)</td>
                <td>
                    <a asp-action="Edit" asp-route-id="@item.Id">Edit</a> |
                    <a asp-action="Details" asp-route-id="@item.Id">Details</a>
                </td>
            </tr>
        }
    </tbody>
</table>
```

2. **Create.cshtml**

```cshtml
@model YourProjectName.Models.Customer

@{
    ViewData["Title"] = "Create Customer";
}

<h1>Create New Customer</h1>

<form asp-action="Create">
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>

    <div class="form-group mb-3">
        <label asp-for="FullName" class="control-label"></label>
        <input asp-for="FullName" class="form-control" />
        <span asp-validation-for="FullName" class="text-danger"></span>
    </div>

    <div class="form-group mb-3">
        <label asp-for="Email" class="control-label"></label>
        <input asp-for="Email" class="form-control" />
        <span asp-validation-for="Email" class="text-danger"></span>
    </div>

    <div class="form-group mb-3">
        <label asp-for="Phone" class="control-label"></label>
        <input asp-for="Phone" class="form-control" />
        <span asp-validation-for="Phone" class="text-danger"></span>
    </div>

    <div class="form-group mb-3">
        <label asp-for="Address" class="control-label"></label>
        <textarea asp-for="Address" class="form-control"></textarea>
        <span asp-validation-for="Address" class="text-danger"></span>
    </div>

    <div class="form-group">
        <input type="submit" value="Create" class="btn btn-success" />
        <a asp-action="Index" class="btn btn-secondary">Back to List</a>
    </div>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

3. **Edit.cshtml** (very similar to Create)

```cshtml
@model YourProjectName.Models.Customer

@{
    ViewData["Title"] = "Edit Customer";
}

<h1>Edit Customer</h1>

<form asp-action="Edit">
    <input type="hidden" asp-for="Id" />
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>

    <!-- Same form fields as Create.cshtml -->
    <div class="form-group mb-3">
        <label asp-for="FullName" class="control-label"></label>
        <input asp-for="FullName" class="form-control" />
        <span asp-validation-for="FullName" class="text-danger"></span>
    </div>

    <!-- ... add Email, Phone, Address the same way ... -->

    <div class="form-group">
        <input type="submit" value="Save" class="btn btn-primary" />
        <a asp-action="Index" class="btn btn-secondary">Cancel</a>
    </div>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

4. **Details.cshtml**

```cshtml
@model YourProjectName.Models.Customer

@{
    ViewData["Title"] = "Customer Details";
}

<h1>Customer Details</h1>

<div>
    <dl class="row">
        <dt class="col-sm-3">@Html.DisplayNameFor(model => model.FullName)</dt>
        <dd class="col-sm-9">@Html.DisplayFor(model => model.FullName)</dd>

        <dt class="col-sm-3">@Html.DisplayNameFor(model => model.Email)</dt>
        <dd class="col-sm-9">@Html.DisplayFor(model => model.Email)</dd>

        <dt class="col-sm-3">@Html.DisplayNameFor(model => model.Phone)</dt>
        <dd class="col-sm-9">@Html.DisplayFor(model => model.Phone)</dd>

        <dt class="col-sm-3">@Html.DisplayNameFor(model => model.Address)</dt>
        <dd class="col-sm-9">@Html.DisplayFor(model => model.Address)</dd>

        <dt class="col-sm-3">@Html.DisplayNameFor(model => model.RegistrationDate)</dt>
        <dd class="col-sm-9">@Html.DisplayFor(model => model.RegistrationDate)</dd>
    </dl>
</div>

<div>
    <a asp-action="Edit" asp-route-id="@Model.Id" class="btn btn-warning">Edit</a> |
    <a asp-action="Index" class="btn btn-secondary">Back to List</a>
</div>
```

---

### Step 4: Test the Lab

1. Run the project (`dotnet run` or F5 in Visual Studio).
2. Navigate to `/Customers` (or add a link in `_Layout.cshtml` or Home page).
3. You should see:
   - Index: list of customers
   - Create: form to add new
   - Edit: update existing
   - Details: view one customer

### Bonus: Use Scaffolding (Faster Way – Recommended for Lab)

If your project already has EF Core + DbContext configured (from later lessons):

1. Right-click **Controllers** → **Add** → **New Scaffolded Item...**
2. Choose **MVC Controller with views, using Entity Framework**
3. Select:
   - Model class: Customer
   - Data context class: YourDbContext
4. Click **Add**
