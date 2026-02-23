# ASP.NET Core MVC -- Host, Kestrel, Middleware, and Request Pipeline

## Host

In ASP.NET Core MVC, the **Host** refers to the environment and
infrastructure that manages the lifecycle of your application.\
It acts as the container that prepares everything required for the
application to run.

### Key Responsibilities of the Host

-   **Application Lifecycle Management**
    -   Starts the application
    -   Keeps it running
    -   Handles graceful shutdown
-   **Dependency Injection Setup**
    -   Registers and manages services
-   **Configuration Management**
    -   Loads settings from `appsettings.json`, environment variables,
        etc.
-   **Middleware Pipeline Setup**
    -   Defines how HTTP requests flow through middleware components
-   **Logging**
    -   Provides built-in logging infrastructure

### Example

``` csharp
var builder = WebApplication.CreateBuilder(args);

// Configure services
builder.Services.AddControllersWithViews();

var app = builder.Build();

// Configure middleware pipeline
app.UseRouting();
app.MapDefaultControllerRoute();

app.Run();
```

### Explanation

-   `CreateBuilder()` → Sets up the Host (services, configuration,
    logging)
-   `Build()` → Finalizes the Host
-   `Run()` → Starts the Host and keeps the application running

------------------------------------------------------------------------

## Kestrel Web Server

By default, ASP.NET Core applications run on **Kestrel**, a
cross-platform web server.

------------------------------------------------------------------------

## Sequence of Kestrel in ASP.NET Core MVC

### 1. Application Startup

-   Running `dotnet run` creates the Host.
-   Host sets up configuration, dependency injection, and logging.

### 2. Kestrel Initialization

-   Host configures Kestrel as the web server.
-   Kestrel binds to ports defined in `launchSettings.json`, environment
    variables, or defaults like:
    -   `http://localhost:5000`

### 3. Request Reception

-   Kestrel listens for HTTP/HTTPS requests (GET, POST, PUT, DELETE,
    PATCH).
-   Parses request and forwards it to the middleware pipeline.

### 4. Middleware Pipeline Execution

Middleware runs in sequence: - HTTPS Redirection / HSTS - Static Files -
Routing - Authentication / Authorization - MVC Controllers or Razor
Pages

### 5. Controller / Action Execution

-   Routing selects the correct controller and action method.
-   Dependency Injection provides required services.
-   Action returns response (HTML, JSON, etc.).

### 6. Response Handling

-   Response travels back through middleware.
-   Kestrel sends response to client.

### 7. Lifecycle Management

-   Kestrel continues listening until Host shuts down.
-   Graceful shutdown closes connections properly.

### Flow Diagram

Client Request → Kestrel → Middleware → MVC Controller → Response →
Kestrel → Client

------------------------------------------------------------------------

## Register Services to Container

``` csharp
var builder = WebApplication.CreateBuilder(args);

// Register MVC
builder.Services.AddControllersWithViews();

// Register DbContext
builder.Services.AddDbContext<AppDbContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});

var app = builder.Build();

app.Run();
```

------------------------------------------------------------------------

## HTTP Request Pipeline

The HTTP Request Pipeline is the sequence of components that process an
HTTP request from start to finish.

Browser → ASP.NET Core → Middleware 1 → Middleware 2 → ... → Endpoint →
Response

### Real-World Analogy (Security Gate System)

1.  Security check
2.  ID check
3.  Ticket validation
4.  Entry gate

------------------------------------------------------------------------

## Middleware

Middleware is code that runs inside the HTTP request pipeline.

### Middleware Can:

-   Inspect the request
-   Modify request or response
-   Call next middleware
-   Stop the request

### Common Middleware Responsibilities:

-   Authentication
-   Authorization
-   Logging
-   Error handling
-   Routing

------------------------------------------------------------------------

## Endpoints

An Endpoint is the final component that handles a request and produces a
response.

Examples: - MVC Controller Action - Razor Page - Minimal API

### Request Flow

Request\
↓\
Middleware\
↓\
Routing\
↓\
Authorization\
↓\
Endpoint\
↓\
Response

------------------------------------------------------------------------

## Static Files

### UseStaticFiles()

Middleware that serves static files from the `wwwroot` folder.

### MapStaticAssets()

Maps static web assets as endpoints (mainly used in Blazor + Razor
Components hybrid apps).

------------------------------------------------------------------------

## Default Routing

``` csharp
app.MapDefaultControllerRoute()
    .WithStaticAssets();
```

Equivalent to:

``` csharp
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}")
    .WithStaticAssets();
```

### Explanation

-   `MapDefaultControllerRoute()` is a convenience method that maps the
    standard MVC default route.
-   `MapControllerRoute()` allows full customization of routing.

**Statement:**\
"MapDefaultControllerRoute() is a convenience method that maps the
standard MVC default route, while MapControllerRoute() allows full
customization of controller routing."

✔ True
