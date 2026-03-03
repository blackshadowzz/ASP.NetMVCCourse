
# Main Database Tables Overview

```
Users
Roles
Customers
Categories
Products
Carts
CartItems
Orders
OrderDetails
Payments
```

---

# 1️⃣ Users Table

Stores customer and admin accounts.

| Column Name  | Data Type     | Description           |
| ------------ | ------------- | --------------------- |
| UserId       | int (PK)      | Primary Key           |
| FullName     | nvarchar(100) | User full name        |
| Email        | nvarchar(100) | Login email           |
| PasswordHash | nvarchar(255) | Hashed password       |
| Phone        | nvarchar(20)  | Phone number          |
| Address      | nvarchar(255) | Shipping address      |
| RoleId       | int (FK)      | Link to Roles         |
| CreatedAt    | datetime      | Account creation date |
| IsActive     | bit           | Active status         |

---

# 2️⃣ Roles Table

| Column   | Type         | Description      |
| -------- | ------------ | ---------------- |
| RoleId   | int (PK)     |                  |
| RoleName | nvarchar(50) | Admin / Customer |

Example Data:

| RoleId | RoleName |
| ------ | -------- |
| 1      | Admin    |
| 2      | Customer |

---

# 3️⃣ Categories Table

Used to group products.

| Column       | Type          |
| ------------ | ------------- |
| CategoryId   | int (PK)      |
| CategoryName | nvarchar(100) |
| Description  | nvarchar(255) |
| IsActive     | bit           |

Example:

* Electronics
* Clothing
* Shoes

---

# 4️⃣ Products Table

Main product information.

| Column      | Type          |
| ----------- | ------------- |
| ProductId   | int (PK)      |
| ProductName | nvarchar(150) |
| Description | nvarchar(max) |
| Price       | decimal(18,2) |
| StockQty    | int           |
| ImageUrl    | nvarchar(255) |
| CategoryId  | int (FK)      |
| CreatedAt   | datetime      |
| IsActive    | bit           |

Relationship:

* One Category → Many Products

---

# 5️⃣ Carts Table

Each user has one cart.

| Column    | Type     |
| --------- | -------- |
| CartId    | int (PK) |
| UserId    | int (FK) |
| CreatedAt | datetime |

---

# 6️⃣ CartItems Table

Stores products inside cart.

| Column     | Type          |
| ---------- | ------------- |
| CartItemId | int (PK)      |
| CartId     | int (FK)      |
| ProductId  | int (FK)      |
| Quantity   | int           |
| UnitPrice  | decimal(18,2) |

Relationship:

* One Cart → Many CartItems
* One Product → Many CartItems

---

# 7️⃣ Orders Table

Created when customer checks out.

| Column          | Type          |
| --------------- | ------------- |
| OrderId         | int (PK)      |
| OrderNumber     | nvarchar(20)  |
| UserId          | int (FK)      |
| OrderDate       | datetime      |
| TotalAmount     | decimal(18,2) |
| Status          | nvarchar(50)  |
| ShippingAddress | nvarchar(255) |

Example Status:

* Pending
* Paid
* Shipped
* Completed
* Cancelled

---

# 8️⃣ OrderDetails Table

Stores each product inside an order.

| Column        | Type          |
| ------------- | ------------- |
| OrderDetailId | int (PK)      |
| OrderId       | int (FK)      |
| ProductId     | int (FK)      |
| Quantity      | int           |
| UnitPrice     | decimal(18,2) |
| SubTotal      | decimal(18,2) |

Relationship:

* One Order → Many OrderDetails

---

# 9️⃣ Payments Table (Simulation)

| Column        | Type          |
| ------------- | ------------- |
| PaymentId     | int (PK)      |
| OrderId       | int (FK)      |
| PaymentMethod | nvarchar(50)  |
| PaymentDate   | datetime      |
| Amount        | decimal(18,2) |
| PaymentStatus | nvarchar(50)  |

Example PaymentMethod:

* Cash on Delivery
* ABA Transfer
* Card (Simulation)

---

# Relationship Summary

```
Roles → Users
Users → Carts
Users → Orders
Categories → Products
Carts → CartItems
Orders → OrderDetails
Orders → Payments
Products → CartItems
Products → OrderDetails
```

---

# ER Diagram (Conceptual View)

```
Users (1) ---- (M) Orders
Users (1) ---- (1) Carts
Categories (1) ---- (M) Products
Orders (1) ---- (M) OrderDetails
Products (1) ---- (M) OrderDetails
Orders (1) ---- (1) Payments
```
