# 📊 Online Store SQL Project

## Table of Contents

1. [Project Overview](#1-project-overview)  
2. [Requirements](#2-requirements)  
3. [Database Design](#3-database-design)  
   - [Step 1: Analyzing the Requirements](#step-1-analyzing-the-requirements)  
   - [Step 2: Identifying Relationships](#step-2-identifying-relationships)  
   - [Step 3: Designing ERD](#step-3-designing-erd)  
   - [Step 4: Designing the Relational Schema](#step-4-designing-the-relational-schema)  
   - [Step 5: Writing SQL Code](#step-5-writing-sql-code)  

---

## 1. Project Overview

This project aims to develop a database management system for an Online Store. The system will manage products, customer data, orders, payments, shipping, and product reviews. The primary goal is to facilitate online shopping, order processing, and feedback management.

### Objectives

- Efficiently manage the product catalog and stock availability.
- Handle customer profiles, including registration and login information.
- Track orders, payments, and shipping status.
- Store customer reviews and product ratings.

---

## 2. Requirements

### Product Catalog Management:
- Store product details such as name, description, price, quantity in stock, and images.
- Categorize products for easier management.

### Customer Information:
- Maintain registered customer data including name, contact information, address, and login credentials.

### Order Information:
- Record customer orders with details like order number, order date, product details, quantity, price, and order status.
- Track the current status of each order (Pending, Processing, Shipped, Delivered, Cancelled, Refunded).

### Payment Transaction:
- Log transaction details including payment ID, amount, method, and timestamp.
- Link payments to respective orders.

### Shipping:
- Manage shipping details, including carrier, tracking number, shipping status, estimated and actual delivery dates.
- Track the shipping status: Processing, Out for Delivery, Delivered, Return to Sender, On Hold, Delayed, Lost.

### Reviews and Ratings:
- Store customer feedback on products with review text, rating (1 to 5), and timestamp.

---

## 3. Database Design

### Step 1: Analyzing the Requirements

The database will include the following entities:
- Customers
- ProductCatalog
- ProductCategory
- Orders
- OrderItems
- Payments
- Shippings
- Reviews
- ProductImages

### Step 2: Identifying Relationships

- Customers and Orders: One-to-Many
- Orders and OrderItems: One-to-Many
- Products and ProductImages: One-to-Many
- Orders and Payments: One-to-One
- Orders and Shippings: One-to-One
- Customers and Reviews: One-to-Many

### Step 3: Designing ERD

The ERD diagram visually represents the relationships between entities (see attached diagram).

### Step 4: Designing the Relational Schema

- Customers(CustomerID, Name, Email, Phone, Address, Username, Password)
- ProductCategory(CategoryID, CategoryName)
- ProductCatalog(ProductID, ProductName, Description, Price, QuantityInStock, CategoryID)
- ProductImages(ImageID, ImageURL, ProductID, ImageOrder)
- Orders(OrderID, CustomerID, OrderDate, TotalAmount, Status)
- OrderItems(OrderID, ProductID, Quantity, Price, TotalItemsPrice)
- Payments(PaymentID, OrderID, Amount, PaymentMethod, TransactionDate)
- Shippings(ShippingID, OrderID, CarrierName, TrackingNumber, ShippingStatus, EstimatedDeliveryDate, ActualDeliveryDate)
- Reviews(ReviewID, ProductID, CustomerID, ReviewText, Rating, ReviewDate)

### Step 5: Writing SQL Code

```sql
-- Add Foreign Keys to ProductCatalog
ALTER TABLE ProductCatalog
ADD CONSTRAINT FK_ProductCatalog_CategoryID
FOREIGN KEY (CategoryID) REFERENCES ProductCategory(CategoryID);

-- Add Foreign Keys to ProductImages
ALTER TABLE ProductImages
ADD CONSTRAINT FK_ProductImages_ProductID
FOREIGN KEY (ProductID) REFERENCES ProductCatalog(ProductID);

-- Add Foreign Keys to Orders
ALTER TABLE Orders
ADD CONSTRAINT FK_Orders_CustomerID
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID);

-- Add Foreign Keys to OrderItems
ALTER TABLE OrderItems
ADD CONSTRAINT FK_OrderItems_OrderID
FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
ADD CONSTRAINT FK_OrderItems_ProductID
FOREIGN KEY (ProductID) REFERENCES ProductCatalog(ProductID);

-- Add Foreign Keys to Payments
ALTER TABLE Payments
ADD CONSTRAINT FK_Payments_OrderID
FOREIGN KEY (OrderID) REFERENCES Orders(OrderID);

-- Add Foreign Keys to Shippings
ALTER TABLE Shippings
ADD CONSTRAINT FK_Shippings_OrderID
FOREIGN KEY (OrderID) REFERENCES Orders(OrderID);

-- Add Foreign Keys to Reviews
ALTER TABLE Reviews
ADD CONSTRAINT FK_Reviews_ProductID
FOREIGN KEY (ProductID) REFERENCES ProductCatalog(ProductID),
ADD CONSTRAINT FK_Reviews_CustomerID
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID);
```