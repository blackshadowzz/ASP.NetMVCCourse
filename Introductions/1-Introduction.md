# RY Mang

![RY Mang Profile Photo](profile.png)

## Education

- **Master of Science in IT**  
  Build Bridge University, Phnom Penh _(Ongoing)_
- **Bachelor of Science in Information Technology** – BBU  
  Graduated: 2024

## Professional Status

- Freelancer

## Contact Information

- **Tel (Telegram):** 092 20 15 44
- **E-mail:** rymang66@gmail.com
- **Portfolio:** [blackshadowzz.github.io](https://blackshadowzz.github.io/)
- **GitHub:** [github.com/blackshadowzz](https://github.com/blackshadowzz)

---

# ASP.NET Core MVC

ASP.NET Core MVC is a web application framework developed by Microsoft for building modern, dynamic, and scalable web applications using C# and .NET.

---

## Instructor

**Mr. RY Mang**

---

## Course Details

- **Subject:** E-Commerce (ASP.NET Core MVC)
- **Course Length:** 30 sessions, 45 hours
- **Location:**
- **Schedule:**

---

# ជំពូកទី ១ - ការណែនាំអំពី ASP.NET Core MVC

## 1.1 តើ ASP.NET Core MVC គឺជាអ្វី?

- **ASP.NET Core MVC** គឺជា Web Application Framework ដែលអភិវឌ្ឍដោយ Microsoft សម្រាប់បង្កើត Web Application ទំនើប (Modern), មានអន្តរកម្ម (Dynamic) និងអាចពង្រីកបាន (Scalable) ដោយប្រើភាសា C# និង .NET។

- ASP.NET Core MVC គឺជា Framework កម្រិតវិជ្ជាជីវៈ ដែលប្រើសម្រាប់បង្កើត Web Application មានសុវត្ថិភាព (Secure), ល្បឿនលឿន (Fast) និងអាចពង្រីកបាន (Scalable) ដោយបែងចែក **ទិន្នន័យ (Data), តក្កវិជ្ជា (Logic), និងផ្ទាំងបង្ហាញ (UI)** តាមគំរូ **MVC (Model-View-Controller)**។

---

### សង្ខេបខ្លី

ASP.NET Core MVC ជា Framework សម្រាប់អភិវឌ្ឍ Web ដែលមានរចនាសម្ព័ន្ធច្បាស់លាស់ អនុញ្ញាតឲ្យអ្នកអភិវឌ្ឍបែងចែកការងារទៅជាផ្នែកៗ ដើម្បីធ្វើឲ្យកម្មវិធីមានប្រសិទ្ធភាព ងាយថែទាំ និងងាយពង្រីកនៅពេលអនាគត។
.

## 1.2 របៀបដែល ASP.NET Core MVC ដំណើរការ

ASP.NET Core MVC អនុវត្តតាម **គំរូរចនា MVC (Model-View-Controller)** ដែលបែងចែកការងារជា ៣ ផ្នែកសំខាន់ៗ៖

---

### **M – Model**

តំណាងឱ្យទិន្នន័យ និង business logic របស់កម្មវិធី។

- គ្រប់គ្រងប្រតិបត្តិការទាក់ទងនឹង Database, Validation និងច្បាប់អាជីវកម្ម (Business Rules)។
- ឧទាហរណ៍៖ `Product` model ដែលកំណត់លក្ខណៈសម្បត្តិ (properties) របស់ផលិតផល និងធ្វើអន្តរកម្មជាមួយ Database។

---

### **V – View**

តំណាងឱ្យផ្ទាំងបង្ហាញ (User Interface - UI)។

- បង្ហាញទិន្នន័យទៅអ្នកប្រើប្រាស់ដោយប្រើ HTML និង Razor Syntax។
- ឧទាហរណ៍៖ `ProductList.cshtml` view ដែលបង្ហាញបញ្ជីផលិតផលក្នុងតារាង (table)។

---

### **C – Controller**

គ្រប់គ្រងការទទួល Request និងការឆ្លើយតប (Response)។

- ជាស្រទាប់កណ្ដាលរវាង Model និង View។
- ឧទាហរណ៍៖ `ProductController` ដែលទទួល Request ពីអ្នកប្រើប្រាស់ ទាញយកទិន្នន័យពី Model ហើយផ្ញើទៅ View។

---

## លក្ខណៈពិសេសសំខាន់ៗរបស់ ASP.NET Core

- **ដំណើរការបានច្រើនប្រព័ន្ធ (Cross-platform)**: អាចដំណើរការលើ Windows, Linux និង macOS។
- **ប្រសិទ្ធភាពខ្ពស់ (High performance)**: ត្រូវបានបង្កើតឡើងឲ្យមានល្បឿនលឿន និងអាចពង្រីកបាន (Scalable)។
- **Open-source**: ប្រើប្រាស់ដោយឥតគិតថ្លៃ និងមានការចូលរួមអភិវឌ្ឍពី Microsoft និងសហគមន៍អ្នកអភិវឌ្ឍ។
- **ស្ថាបត្យកម្មម៉ូឌុល (Modular architecture)**: អាចបន្ថែមតែ package ដែលចាំបាច់ ធ្វើឲ្យកម្មវិធីមានទំហំស្រាល។
- **Dependency Injection មានស្រាប់**: ជួយគ្រប់គ្រងសេវាកម្ម (services) ឲ្យមានប្រសិទ្ធភាព និងងាយស្រួលក្នុងការធ្វើតេស្ត។
- **Unified development model**: អាចបង្កើត Web Apps, APIs និង Microservices ក្នុង Framework តែមួយ។

---

## ហេតុអ្វីបានជា MVC សំខាន់?

- **Separation of Concerns**: បែងចែកការទទួលខុសត្រូវច្បាស់លាស់រវាង Model, View និង Controller។
- **Maintainability**: ងាយស្រួលកែប្រែ ឬជួសជុលផ្នែកណាមួយ ដោយមិនប៉ះពាល់ដល់ផ្នែកផ្សេង។
- **Testability**: អាចធ្វើតេស្ត Business Logic នៅក្នុង Model និង Controller ដោយឡែកពី UI។
- **Scalability**: កម្មវិធីអាចពង្រីកបន្ថែមមុខងារ ដោយនៅតែមានរចនាសម្ព័ន្ធរៀបរយ។

---

## ឧទាហរណ៍លំហូរការងារ (Example Flow)

1. អ្នកប្រើប្រាស់ស្នើសុំ URL `/Products/List`។
2. **Controller** (`ProductController`) ទទួល Request។
3. Controller ហៅ **Model** (`Product`) ដើម្បីទាញយកទិន្នន័យផលិតផល។
4. Controller ផ្ញើទិន្នន័យទៅ **View** (`ProductList.cshtml`)។
5. View បង្កើត HTML ហើយផ្ញើត្រឡប់ទៅ Browser របស់អ្នកប្រើប្រាស់។

---

## 1.3 គោលបំណង (Purpose) របស់ ASP.NET Core MVC

ASP.NET Core MVC ត្រូវបានប្រើសម្រាប់៖

✅ **បង្កើត Web Application ដែលមានរចនាសម្ព័ន្ធច្បាស់លាស់**

- ប្រើគំរូ MVC ដើម្បីរៀបចំកូដឲ្យមានលំដាប់ និងមានស្តង់ដារ។

✅ **បែងចែក Logic, UI និង Data ឲ្យដាច់ពីគ្នាច្បាស់លាស់**

- Logic (Business Logic) នៅក្នុង Model/Controller
- UI នៅក្នុង View
- Data Layer តភ្ជាប់ជាមួយ Database

✅ **បង្កើតកម្មវិធីមានសុវត្ថិភាព (Secure Applications)**

- គាំទ្រ Authentication និង Authorization
- ការពារ CSRF, XSS និងការវាយប្រហារផ្សេងៗ

✅ **គាំទ្រប្រព័ន្ធធំៗ (Large-scale Systems)**

- អាចប្រើសម្រាប់ប្រព័ន្ធ E-commerce
- ERP (Enterprise Resource Planning)
- Banking System
- Enterprise Applications ផ្សេងៗ

✅ **អាចភ្ជាប់ជាមួយបច្ចេកវិទ្យាផ្សេងៗបានយ៉ាងងាយស្រួល**

- SQL Server
- Entity Framework Core
- REST APIs
- Authentication & Authorization Services

---

## 1.4 ហេតុអ្វីបានជា Developer ប្រើ ASP.NET Core MVC

### ✅ អត្ថប្រយោជន៍ (Advantages)

**1. Strongly Typed (កាត់បន្ថយ Runtime Error)**

    * ប្រើភាសា C# ដែលជា Strongly Typed Language
    * កំហុសភាគច្រើនត្រូវបានរកឃើញនៅពេល Compile មុនពេល Run
    * កាត់បន្ថយបញ្ហា Runtime Error

**2. ប្រសិទ្ធភាពខ្ពស់ (High Performance)**

    - ត្រូវបាន Optimize សម្រាប់ល្បឿន និង Scalability
    - សមស្របសម្រាប់ប្រព័ន្ធដែលមានអ្នកប្រើច្រើន

**3. សុវត្ថិភាពមានស្រាប់ (Built-in Security)**

    - មាន Authentication (Login System)
    - មាន Authorization (Role & Permission)
    - ការពារ CSRF, XSS និងការវាយប្រហារផ្សេងៗ

**4. ស្ថាបត្យកម្មស្អាត (Clean Architecture)**

    - អនុវត្តតាម MVC Pattern
    - បែងចែក Model, View និង Controller ច្បាស់លាស់
    - ងាយថែទាំ និងអភិវឌ្ឍបន្ថែម

**5. គាំទ្រកម្រិតសហគ្រាស (Enterprise-level Support)**

    - គាំទ្រដោយ Microsoft
    - សមស្របសម្រាប់ប្រព័ន្ធធំៗ (Enterprise Systems)

**6. ដំណើរការល្អជាមួយ SQL Server**

    - Integration ល្អជាមួយ SQL Server
    - ប្រើ Entity Framework Core សម្រាប់ ORM
    - គ្រប់គ្រង Database បានមានប្រសិទ្ធភាព

---

### ❌ គុណវិបត្តិ (Disadvantages)

**1. កម្រិតសិក្សាខ្ពស់ជាង PHP (Steeper Learning Curve)**

    - MVC Pattern និង .NET Ecosystem មានភាពស្មុគស្មាញជាង PHP
    - ត្រូវការពេលវេលាសិក្សា និងអនុវត្តច្រើន

**2. ត្រូវការចំណេះដឹង .NET**

    - ត្រូវស្គាល់ C#, .NET Runtime និង Framework Structure

**3. តម្លៃ Hosting អាចខ្ពស់ជាង PHP**

    - Shared Hosting សម្រាប់ .NET អាចមានតម្លៃខ្ពស់ជាង PHP
    - ជាទូទៅត្រូវប្រើ VPS ឬ Cloud Hosting

---
