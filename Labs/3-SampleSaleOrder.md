# Sample Sale Order (ASP.NET Core MVC)

this is a sample sale order for testing purposes. It includes various fields such as customer information, order details, and payment information. The order is created to demonstrate the functionality of the sales order processing system and to provide a template for future orders.

## Prerequisites

1. Create a database and a table for products (if you don't have one already)
2. Create a connection string to connect to your database (you can hard-code it or use appsettings.json)(skip if you already have a connection string in place)
3. Add DbContext and configure it to use your database (if already exists, skip this step)

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }
    ...
}
```

3. Create a CRUD operation for products (if you don't have one already)
4. Create a `OrderCardDto` model for adding products to the order card

```csharp
public class OrderCardDto
{
   public required Product Product { get; set; }
    public int Quantity { get; set; }
    public decimal? SubTotal { get; set; }
}
```

5. Create models `Order` and `OrderLine`

```csharp

    public class Order
    {
        public Guid OrderId { get; set; }
        public int? OrderNumber { get; set; }
        public string? UserId { get; set; }
        public int? TotalItem { get; set; }
        public decimal? TotalAmount { get; set; }
        public string? Status { get; set; } = OrderStatus.Pending.ToString();
        public string? ShippingAddress { get; set; }
        public DateTime? OrderDate { get; set; } = DateTime.Now;
        public List<OrderLine> OrderLines { get; set; } = new List<OrderLine>();
    }
    public enum OrderStatus
    {
        Pending,
        Processing,
        Shipped,
        Delivered,
        Cancelled
    }
    public class OrderLine
    {
        public Guid Id { get; set; }
        public Guid OrderId { get; set; }
        public int? ItemId { get; set; }
        public int? Quantity { get; set; }
        public decimal? UnitPrice { get; set; }
        public decimal? SubTotal { get; set; }
        public Order? Order { get; set; }

    }
```

6. Modify AppDbContext to include DbSet for Order and OrderLine (if you are using Entity Framework Core)

```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }

    public DbSet<Order> Orders { get; set; } // Add this line to include Orders in the DbContext
    public DbSet<OrderLine> OrderLines { get; set; } // Add this line to include OrderLines in the DbContext

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configure the relationship between Order and OrderLine
        modelBuilder.Entity<Order>()
            .HasMany(o => o.OrderLines)
            .WithOne(ol => ol.Order)
            .HasForeignKey(ol => ol.OrderId)
            .OnDelete(DeleteBehavior.Cascade);

        base.OnModelCreating(modelBuilder);
    }
}
```

7. Run the migration and update the database to create the Orders and OrderLines tables

On terminal, run the following commands:

```bash

dotnet ef migrations add AddOrdersAndOrderLines
dotnet ef database update

```

On Package Manager Console, run the following commands:

```powershell

Add-Migration AddOrdersAndOrderLines
Update-Database

```

after running the above commands successfully, you should see the Orders and OrderLines tables created in your database. You can now proceed to implement the functionality for creating and managing sales orders in your application.

---

### Create Services for Order Management

To manage orders effectively, you can create a service class that encapsulates the business logic related to order processing. This service will handle operations such as creating new orders, retrieving order details, updating order status, and deleting orders.

Add new Interface: `Services/IOrderService.cs`

```csharp
public interface IOrderService
{
    Task<Order?> GetOrderByIdAsync(Guid id);
    Task<IEnumerable<Order>> ListOrdersAsync(string? userId = null,string? filter=null);
    Task CreateOrderAsync(Order order);
    Task UpdateOrderAsync(Order order);
    Task DeleteOrderAsync(Guid id);
}
```

Add new Service Implementation: `Services/OrderService.cs`

```csharp
public class OrderService : IOrderService
{
    private readonly AppDbContext _context;

    public OrderService(AppDbContext context)
    {
        _context = context;
    }

    public async Task<Order?> GetOrderByIdAsync(Guid id)
    {
        return await _context.Orders.Include(o => o.OrderLines).FirstOrDefaultAsync(o => o.OrderId == id);
    }

    public async Task<IEnumerable<Order>> ListOrdersAsync(string? userId = null,string? filter=null)
    {
        var query = _context.Orders.Include(o => o.OrderLines).AsQueryable();

        if (!string.IsNullOrEmpty(userId))
        {
            query = query.Where(o => o.UserId == userId);
        }

        if (!string.IsNullOrEmpty(filter))
        {
            query = query.Where(o => o.OrderNumber.Contains(filter));
        }

        return await query.ToListAsync();
    }

    public async Task CreateOrderAsync(Order order)
    {
        _context.Orders.Add(order);
        await _context.SaveChangesAsync();
    }

    public async Task UpdateOrderAsync(Order order)
    {
        _context.Orders.Update(order);
        await _context.SaveChangesAsync();
    }

    public async Task DeleteOrderAsync(Guid id)
    {
        var order = await GetOrderByIdAsync(id);
        if (order != null)
        {
            _context.Orders.Remove(order);
            await _context.SaveChangesAsync();
        }
    }
}
```

### Register the OrderService in Program.cs

In your Program.cs file, register the OrderService with the dependency injection container:

```csharp
// Add services to the container.
builder.Services.AddControllersWithViews();

// Inject OrderService
builder.Services.AddScoped<IOrderService, OrderService>(); // Add this line to register the OrderService

// Add session services for managing user sessions and storing order information temporarily
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

```

### Next Steps

create a new controller named `OrdersController.cs` and implement the necessary action methods to handle the creation, viewing, editing, and deletion of orders. You can also create corresponding views for these actions to allow users to interact with the order management system through a user-friendly interface.

Add Controller: `Controllers/OrdersController.cs`

```csharp
public class OrdersController : Controller
{
    private const string CartSessionKey = "CartItemIds";
    private readonly IOrderService _orderService;

    public OrdersController(IOrderService orderService)
    {
        _orderService = orderService;
    }

    // Implement action methods for Views products, create order, view order details, edit order, delete order, etc.

}
```

Add Index action method to list all Products and add to cart

```csharp
public IActionResult Index(string? filter = "")
{
    var keyword = filter?.Trim() ?? string.Empty;

    var items = dbContext.Products.AsQueryable();
    if (!string.IsNullOrWhiteSpace(keyword))
    {
        items = items.Where(x => x.Name.ToLower().Contains(keyword.ToLower()));
    }

    var sales = items
        .OrderBy(x => x.Name)
        .ToList();

    ViewBag.Filter = keyword;
    ViewBag.Sales = sales;
    ViewBag.Cart = GetCartItems();

    return View();
}
```

Add method GetCartItems to retrieve the items in the cart from the session

```csharp
private List<OrderCardDto> GetCartItems()
{
    // Get the item IDs from the cart stored in the session
    var cartItemIds = GetCartItemIds();
    // Retrieve the corresponding items from the database
    var cartItems = dbContext.Products
        .Where(item => cartItemIds.Contains(item.Id))
        .ToList();

    // Create a list of OrderCardDto objects to represent
    // the items in the cart along with
    // their quantities and subtotals
    return cartItems
        .Select(item => new OrderCardDto
        {
            Item = item,
            Quantity = cartItemIds.Count(id => id == item.Id),
            SubTotal = item.Price * cartItemIds.Count(id => id == item.Id)
        })
        .OrderBy(line => line.Item.Name)
        .ToList();
}
```

Add method GetCartItemIds to retrieve the item IDs from the session

```csharp
private List<int> GetCartItemIds()
{
    var cartJson = HttpContext.Session.GetString(CartSessionKey);

    if (string.IsNullOrWhiteSpace(cartJson))
    {
        return [];
    }
    return JsonSerializer.Deserialize<List<int>>(cartJson) ?? [];
}
```

Add method AddToCart to add an item to the cart and store it in the session

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult AddToCart(int itemId)
{
    // Get the current cart item IDs from the session,
    // add the new item ID, and save it back to the session
    var cartItemIds = GetCartItemIds();
    cartItemIds.Add(itemId);
    // Save the updated cart item IDs back to the session
    SaveCartItemIds(cartItemIds);

    return RedirectToAction(nameof(Index));
}
```

Add method SaveCartItemIds to save the item IDs to the session

```csharp
private void SaveCartItemIds(List<int> cartItemIds)
{
    var cartJson = JsonSerializer.Serialize(cartItemIds);
    HttpContext.Session.SetString(CartSessionKey, cartJson);
}
```

Add method RemoveFromCart to remove an item from the cart and update the session

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult RemoveFromCart(int itemId)
{
    // Get the current cart item IDs from the session,
    // remove the specified item ID, and save it back to the session
    var cartItemIds = GetCartItemIds();
    cartItemIds.Remove(itemId);
    // Save the updated cart item IDs back to the session
    SaveCartItemIds(cartItemIds);

    return RedirectToAction(nameof(Index));
}
```

Add method ClearCart to clear all items from the cart and update the session

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult ClearCart()
{
    // Clear the cart by removing the session key
    HttpContext.Session.Remove(CartSessionKey);
    return RedirectToAction(nameof(Index));
}
```

---

### Implement Views for Orders

First, create a new folder named `Orders` inside the `Views` folder. Then, create the following views:

- `Index.cshtml`: Display a list of products and allow users to add them to the cart for creating a new order
- `List.cshtml`: Display a list of orders with options to view details, edit, or delete each order
- `Details.cshtml`: Show details of a specific order
- `Edit.cshtml`: Provide a form to edit an existing order if needed

Add method CreateOrder to create a new order based on the items in the cart

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> SaveOrder(Order orderInput)
{
    //Check if the cart is empty before saving the order
    var cartItemIds = GetCartItemIds();
    if (cartItemIds.Count == 0)
    {
        TempData["OrderError"] = "Cart is empty.";
        return RedirectToAction(nameof(Index));
    }

    // Create a new Order object and populate it with the order details
    var cardItems = GetCartItems();

    var order = new Order
    {
        OrderDate = DateTime.Now,
        OrderNumber = (dbContext.Orders.Max(o => (int?)o.OrderNumber) ?? 0) + 1,
        TotalItem = cardItems.Count,
        UserId = orderInput.UserId,
        ShippingAddress = orderInput.ShippingAddress,
        Status = string.IsNullOrWhiteSpace(orderInput.Status) ? "Pending" : orderInput.Status
    };

    // Create OrderLine objects for each item in the cart
    // and calculate the total amount
    order.OrderLines = cardItems.Select(ci => new OrderLine
    {
        ItemId = ci.Item.Id,
        UnitPrice = ci.Item.Price,
        Quantity = ci.Quantity,
        SubTotal = ci.SubTotal
    }).ToList();

    order.TotalAmount = order.OrderLines.Sum(line => line.SubTotal ?? 0m);

    await _orderService.CreateOrderAsync(order);

    HttpContext.Session.Remove(CartSessionKey);
    TempData["OrderSuccess"] = $"Order #{order.OrderNumber} saved.";

    return RedirectToAction(nameof(Index));
}
```

This implementation provides a basic structure for managing sales orders in an ASP.NET Core MVC application. You can more methods to view order details, edit orders, and delete orders as needed. Additionally, you can enhance the user interface to provide a better user experience when interacting with the order management system.

---

#### Sample view for Index.cshtml to display products and manage the cart

```cshtml
@{
    ViewData["Title"] = "Index";
    List<Item> sales = ViewBag.Sales as List<Item> ?? new();
    List<OrderCardDto> cart = ViewBag.Cart as List<OrderCardDto> ?? new();
    string filter = ViewBag.Filter as string ?? string.Empty;
    decimal cartTotal = cart.Sum(line => (line.Item.Price ?? 0m) * line.Quantity);
}
<div class="container-fluid">
    <div class="d-flex justify-content-between align-items-center mb-3">
        <h4 class="mb-0">Sales</h4>
        <span class="badge rounded-pill text-bg-light border fs-6">@sales.Count item(s)</span>
    </div>

    @if (TempData["OrderSuccess"] is string successMessage)
    {
        <div class="alert alert-success">@successMessage</div>
    }

    @if (TempData["OrderError"] is string errorMessage)
    {
        <div class="alert alert-danger">@errorMessage</div>
    }


    <div class="row">
        <div class="col-md-8">
            <div class="card shadow-sm border-0 sticky-top" style="top: 1rem;">
                <div class="card-body border-bottom bg-light-subtle">
                    <form asp-action="Index" asp-controller="Sales" method="get" class="row g-2 align-items-end">
                        <div class="col-md-8">
                            <label for="filter" class="form-label fw-semibold mb-1">Search items</label>
                            <input id="filter" name="filter" type="text" class="form-control" value="@filter" placeholder="Type item name..." />
                        </div>
                        <div class="col-md-4 d-grid">
                            <button type="submit" class="btn btn-dark">Search</button>
                        </div>
                        @if (!string.IsNullOrWhiteSpace(filter))
                        {
                            <div class="col-12">
                                <a asp-action="Index" asp-controller="Sales" class="btn btn-link p-0">Clear search</a>
                            </div>
                        }
                    </form>
                </div>
                <div id="itemList" class="p-3 overflow-auto" style="max-height: calc(100vh - 230px);">
                    @if (sales.Count == 0)
                    {
                        <div class="text-center py-5">
                            <p class="text-muted mb-0">No matching items found.</p>
                        </div>
                    }
                    else
                    {
                        <div class="row row-cols-1 row-cols-md-4 g-4">
                            @foreach (var item in sales)
                            {
                                <div class="col">
                                    <div class="card">
                                        <img src="..." class="card-img-top" alt="...">
                                        <div class="card-body">
                                            <div>
                                                <h5 class="card-title mb-1">@item.Name</h5>
                                                <p class="card-text text-muted mb-0">Price: $@((item.Price ?? 0m).ToString("0.00"))</p>
                                            </div>
                                            <span class="badge text-bg-light border">#@item.Id</span>
                                        </div>
                                        <div class="card-footer bg-transparent border-0 pt-0 pb-3">
                                            <form asp-action="AddToCart" asp-controller="Sales" method="post">
                                                <input type="hidden" name="itemId" value="@item.Id" />
                                                <button type="submit" class="btn btn-primary w-100">Add to Cart</button>
                                            </form>
                                        </div>
                                    </div>
                                </div>

                            }
                        </div>
                    }
                </div>
            </div>
        </div>
        <div class="col-md-4">
            <div class="card shadow-sm sticky-top" style="top: 1rem;">
                <div class="card-header bg-primary text-white">
                    <h5 class="mb-0">Your Cart</h5>
                </div>
                <div class="card-body">
                    @if (cart.Count == 0)
                    {
                        <p class="text-muted mb-0">No items in cart yet.</p>
                    }
                    else
                    {
                        <ul class="list-group list-group-flush mb-3">
                            @foreach (var line in cart)
                            {
                                <li class="list-group-item px-0">
                                    <div class="d-flex justify-content-between align-items-center">
                                        <div>
                                            <div class="fw-semibold">@line.Item.Name</div>
                                            <small class="text-muted">$@((line.Item.Price ?? 0m).ToString("0.00")) each</small>
                                        </div>
                                        <div>
                                            <div class="d-flex align-items-center gap-2 mt-2">
                                                <form asp-action="RemoveFromItem" asp-controller="Sales" method="post" class="m-0">
                                                    <input type="hidden" name="itemId" value="@line.Item.Id" />
                                                    <button type="submit" class="btn btn-sm btn-outline-secondary" title="Decrease quantity">-</button>
                                                </form>

                                                <span class="badge text-bg-light border">Qty: @line.Quantity</span>

                                                <form asp-action="AddToCart" asp-controller="Sales" method="post" class="m-0">
                                                    <input type="hidden" name="itemId" value="@line.Item.Id" />
                                                    <button type="submit" class="btn btn-sm btn-outline-primary" title="Increase quantity">+</button>
                                                </form>
                                            </div>
                                        </div>
                                        <span class="fw-semibold">$@(((line.Item.Price ?? 0m) * line.Quantity).ToString("0.00"))</span>
                                    </div>


                                </li>
                            }
                        </ul>
                        <div class="d-flex justify-content-between border-top pt-3">
                            <span class="fw-bold">Total</span>
                            <span class="fw-bold text-primary">$@cartTotal.ToString("0.00")</span>
                        </div>

                        <form asp-action="SaveOrder" asp-controller="Sales" method="post" class="mt-3">
                            <div class="mb-2">
                                <label class="form-label mb-1">Customer ID</label>
                                <input type="text" name="UserId" class="form-control" placeholder="customer_001" />
                            </div>
                            <div class="mb-2">
                                <label class="form-label mb-1">Shipping Address</label>
                                <textarea name="ShippingAddress" class="form-control" rows="2" placeholder="Enter shipping address"></textarea>
                            </div>
                            <div class="mb-3">
                                <label class="form-label mb-1">Order Status</label>
                                <select name="Status" class="form-select">
                                    <option value="Pending" selected>Pending</option>
                                    <option value="Paid">Paid</option>
                                    <option value="Processing">Processing</option>
                                </select>
                            </div>
                            <div class="d-grid gap-2">
                                <button type="submit"
                                        onclick="return confirm('Are you sure you want to save this order?');"
                                        class="btn btn-success">
                                    Save Order
                                </button>
                            </div>
                        </form>

                        <form asp-action="ClearCart" asp-controller="Sales" method="post" class="mt-2">
                            <div class="d-grid">
                                <button type="submit" class="btn btn-outline-secondary">Clear Cart</button>
                            </div>
                        </form>
                    }
                </div>
            </div>

        </div>
    </div>

</div>
```
