# 1️⃣ Introduction Origins of .NET

## 📌 .NET Framework

- **Initial Release:** 2002
- **Platform:** Windows-only
- **Primary Languages:** C#, VB.NET, F# (also supports others via CLR)

.NET Framework was Microsoft’s original managed runtime platform designed primarily for Windows application development.

---

### 🔧 Core Architecture

#### 1. CLR (Common Language Runtime)

The execution engine of .NET Framework. It provides:

- Memory management (Garbage Collection)
- Exception handling
- Security enforcement (Code Access Security)
- JIT (Just-In-Time) compilation
- Thread management
- Type safety

#### 2. BCL (Base Class Library)

Provides fundamental APIs:

- Collections
- File I/O
- Networking
- XML/JSON processing
- LINQ
- Reflection
- Threading

#### 3. Application Models

- **ASP.NET (Web Forms, MVC, Web API)** – Web development
- **Windows Forms (WinForms)** – Desktop UI framework
- **WPF (Windows Presentation Foundation)** – Advanced desktop UI
- **WCF (Windows Communication Foundation)** – Service communication
- **Entity Framework** – ORM for database access

---

### 📚 Versions Timeline

| Version   | Year                      |
| --------- | ------------------------- |
| 1.0       | 2002                      |
| 1.1       | 2003                      |
| 2.0       | 2005                      |
| 3.0       | 2006                      |
| 3.5       | 2007                      |
| 4.0       | 2010                      |
| 4.5–4.8   | 2012–2019                 |
| **4.8.1** | 2022 (final version line) |

> ⚠️ .NET Framework is now in maintenance mode. No major new features will be added.

---

### ⚠️ Limitations

- Windows-only
- Monolithic installation
- Performance constraints compared to modern runtime
- Not optimized for cloud-native architectures

---

# 2️⃣ .NET Core (Modern Cross-Platform Foundation)

## 📌 Overview

- **First Release:** 2016 (.NET Core 1.0)
- **Platforms:** Windows, Linux, macOS
- **Design Goal:** Cross-platform, modular, high-performance runtime
- **Cloud-native ready**

.NET Core was a complete redesign of .NET with performance, modularity, and cross-platform support as primary goals.

---

## 🚀 Major Improvements Over .NET Framework

- Modular architecture (via NuGet packages)
- Side-by-side versioning
- Cross-platform CLI (`dotnet`)
- High-performance Kestrel web server
- Container and Docker support
- Improved dependency injection support
- Built-in logging and configuration system

---

## 🏗 Architecture Components

- **CoreCLR** – Runtime engine
- **CoreFX** – Core libraries
- **Roslyn Compiler** – C# & VB compiler
- **Kestrel** – High-performance web server

---

## 📊 .NET Core Versions

| Version | Year | Support | Notes                   |
| ------- | ---- | ------- | ----------------------- |
| 1.0     | 2016 | STS     | Initial release         |
| 1.1     | 2016 | STS     | API expansion           |
| 2.0     | 2017 | STS     | Major API improvements  |
| 2.1     | 2018 | **LTS** | Production stable       |
| 2.2     | 2018 | STS     | Short-term              |
| 3.0     | 2019 | STS     | WinForms/WPF support    |
| 3.1     | 2019 | **LTS** | Final .NET Core version |

> 🔔 After version 3.1, the “Core” branding was removed and merged into unified .NET.

---

# 3️⃣ Unified .NET (.NET 5+)

Microsoft unified:

- .NET Framework
- .NET Core
- Xamarin
- Mono

Into one single platform called **.NET**.

---

## 📌 Version Timeline

| Version | Year | Support | Highlights             |
| ------- | ---- | ------- | ---------------------- |
| .NET 5  | 2020 | STS     | First unified release  |
| .NET 6  | 2021 | **LTS** | MAUI, minimal APIs     |
| .NET 7  | 2022 | STS     | Performance & cloud    |
| .NET 8  | 2023 | **LTS** | AOT, C# 12             |
| .NET 9  | 2024 | STS     | Continued improvements |

---

## 🔑 Key Features of Unified .NET

- Single SDK & runtime
- Cross-platform
- High performance
- Native AOT (Ahead-of-Time compilation)
- Built-in container optimization
- Cloud-native support
- gRPC & microservices ready
- First-class Azure integration

---

## 📦 Modern Application Models

- Web: ASP.NET Core
- Desktop: WinForms, WPF
- Mobile/Desktop Cross-platform: .NET MAUI
- Cloud & Microservices
- Blazor (WebAssembly & Server)
- Worker Services
- IoT

---

# 4️⃣ ASP.NET Evolution

## 📌 ASP.NET (Classic)

- Runs only on .NET Framework
- System.Web dependency
- IIS tightly coupled

---

## 📌 ASP.NET Core

ASP.NET Core is a complete rewrite, not a version upgrade.

- Cross-platform
- High performance
- Lightweight pipeline
- Middleware-based architecture
- Built-in dependency injection
- Unified MVC & Web API

---

# 5️⃣ ASP.NET Core Architecture

## 🔹 Middleware Pipeline

ASP.NET Core processes HTTP requests through middleware components:

```csharp
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
```

Each middleware:

- Receives HTTP request
- Can modify request/response
- Passes control to next middleware

---

## 🔹 MVC Core

Implements **Model-View-Controller** pattern:

- **Model** → Data & business logic
- **View** → UI rendering
- **Controller** → Handles HTTP requests

Benefits:

- Separation of concerns
- Testability
- Maintainability

---

## 🔹 Razor

A templating engine that allows C# inside HTML:

```html
<h1>Hello @Model.Name</h1>
```

Used in:

- MVC Views
- Razor Pages
- Blazor components

---

## 🔹 Razor Pages

- Page-focused model
- Simplified MVC
- Better for CRUD apps

---

## 🔹 Web API

- RESTful API development
- JSON-based communication
- Used for SPA, mobile apps

---

## 🔹 SignalR

Real-time communication library.

Features:

- WebSocket support
- Automatic fallback
- Hub-based communication

Used for:

- Chat apps
- Live dashboards
- Notifications
- Multiplayer apps

---

## 🔹 Blazor

Build interactive web UI using C# instead of JavaScript.

### Hosting Models:

- **Blazor Server** – Runs on server
- **Blazor WebAssembly (WASM)** – Runs in browser
- **Blazor Hybrid** – Desktop/mobile via MAUI

---

# 6️⃣ Modern .NET Ecosystem Overview

| Category          | Technology                    |
| ----------------- | ----------------------------- |
| Web Apps          | ASP.NET Core MVC, Razor Pages |
| SPA               | Blazor WASM                   |
| APIs              | ASP.NET Core Web API          |
| Real-Time         | SignalR                       |
| Desktop           | WPF, WinForms                 |
| Cross-platform UI | .NET MAUI                     |
| Cloud             | Azure, Docker                 |
| Microservices     | gRPC, Web API                 |
| ORM               | Entity Framework Core         |

---

# 7️⃣ LTS vs STS

- **LTS (Long-Term Support)** → 3 years support
- **STS (Short-Term Support)** → 18 months support

Recommended for production:

- .NET 6 (LTS)
- .NET 8 (LTS)

---

# 8️⃣ Summary Evolution Diagram

```
.NET Framework (Windows-only)
        ↓
.NET Core (Cross-platform redesign)
        ↓
Unified .NET (.NET 5+)
```

---
