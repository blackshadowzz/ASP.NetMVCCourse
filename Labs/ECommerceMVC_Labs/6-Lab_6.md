# Lab 6

We do continue from Lab 5
គោលបំណងរួមនៃ Lab 6

- បន្ថែម Action Methods សម្រាប់ Create, Details, Edit, និង Delete នៅក្នុង ProductsController.cs
- បង្កើតទំព័រ View សម្រាប់ Create និង Edit
- ធ្វើឱ្យអ្នកប្រើប្រាស់អាចបន្ថែម មើលលម្អិត កែប្រែ និងលុបផលិតផលបាន

## Step 1:

នៅក្នុង​ ProductsController.cs បន្ថែម action method `Create` `Details` `Edit` `Delete`

```csharp

[HttpGet]
public IActionResult Create()
{
    ViewBag.Categories = new SelectList(categories, "Id", "CategoryName");
    return View();
}

[HttpPost]
public IActionResult Create(Product product)
{
    if (ModelState.IsValid)
    {
        product.Id = products.Any() ? products.Max(p => p.Id) + 1 : 1;
        products.Add(product);
        return RedirectToAction(nameof(Index));
    }
    return View(product);
}

public IActionResult Details(int? id)
{
    if (id == null) return NotFound();

    var product = products.FirstOrDefault(p => p.Id == id);
    if (product == null) return NotFound
    return View(product);
}

[HttpGet]
public IActionResult Edit(int? id)
{
    if (id == null) return NotFound();

    var product = products.FirstOrDefault(p => p.Id == id);
    if (product == null) return NotFound();

    ViewBag.Categories = new SelectList(categories, "Id", "CategoryName", product.CategoryId);
    return View(product);
}

public IActionResult Delete(int? id)
{
    if (id == null) return NotFound();

    var product = products.FirstOrDefault(p => p.Id == id);
    if (product == null) return NotFound();

    products.Remove(product);
    return RedirectToAction(nameof(Index));
}
```

## Step 2:

Create Views/Products/Create.cshtml

```cshtml
@model Product

@{
    ViewData["Title"] = "Create Product";
}
<form asp-action="Create" asp-method="post">
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <div class="form-group mb-3">
        <label asp-for="ProductName" class="control-label"></label>
        <input asp-for="ProductName" class="form-control" />
        <span asp-validation-for="ProductName" class="text-danger"></span>
    </div>
    <div class="form-group mb-2">
        <label asp-for="Price" class="control-label"></label>
        <input asp-for="Price" class="form-control" />
        <span asp-validation-for="Price" class="text-danger"></span>
    </div>
    <div class="form-group mb-2">
        <label asp-for="CategoryId" class="control-label"></label>
        <select asp-for="CategoryId" asp-items="ViewBag.Categories" class="form-control"></select>
        <span asp-validation-for="CategoryId" class="text-danger"></span>
    </div>
    <div class="form-group mb-3">
        <label asp-for="Description" class="control-label"></label>
        <textarea asp-for="Description" class="form-control"></textarea>
        <span asp-validation-for="Description" class="text-danger"></span>
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

## Step 3:

Create Views/Products/Edit.cshtml

```cshtml
@model Product

@{
    ViewData["Title"] = "Edit Product";
}
<form asp-action="Edit" asp-method="post">
    <input type="hidden" asp-for="Id" />
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <div class="form-group mb-3">
        <label asp-for="ProductName" class="control-label"></label>
        <input asp-for="ProductName" class="form-control" />
        <span asp-validation-for="ProductName" class="text-danger"></span>
    </div>
    <div class="form-group mb-2">
        <label asp-for="Price" class="control-label"></label>
        <input asp-for="Price" class="form-control" />
        <span asp-validation-for="Price" class="text-danger"></span>
    </div>
    <div class="form-group mb-2">
        <label asp-for="CategoryId" class="control-label"></label>
        <select asp-for="CategoryId" asp-items="ViewBag.Categories" class="form-control"></select>
        <span asp-validation-for="CategoryId" class="text-danger"></span>
    </div>
    <div class="form-group mb-3">
        <label asp-for="Description" class="control-label"></label>
        <textarea asp-for="Description" class="form-control"></textarea>
        <span asp-validation-for="Description" class="text-danger"></span>
    </div>
    <div class="form-group">
        <input type="submit" value="Save" class="btn btn-success" />
        <a asp-action="Index" class="btn btn-secondary">Back to List</a>
    </div>

</form>

```
