# 📊 Car Rental SQL Project

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

This project aims to develop a database management system for a Car Rental service. The system will manage customer profiles, vehicle inventory, booking processes, rental transactions, and vehicle returns. The primary goal is to efficiently handle vehicle rentals, track transactions, and manage returns.

### Objectives

- Efficiently manage customer profiles and their rental bookings.
- Maintain an up-to-date record of available vehicles.
- Track rental transactions, payments, and returns.
- Calculate fees, refunds, and mileage accurately.

---

## 2. Requirements

### Customer Management:
- Store customer personal information (Name, Contact Information, Driver's License Number).

### Vehicles Information:
- Record vehicle details including Make, Model, Year, Mileage, Plate Number, and Rental Price.
- Maintain vehicle availability and rental status.
- Track vehicle fuel types (Gasoline, Diesel, Electric, Hybrid).
- Categorize vehicles (4x4, Sedan, etc.).

### Vehicle Booking:
- Record rental booking details: customer, vehicle, start date, end date, pickup/drop-off location, initial rental days, and initial check notes.
- Store the initial total due amount for the booking.

### Rental Transaction:
- Track rental payment details and initial amount paid.
- Log transactions including rental start, end, and payment updates.

### Vehicle Return:
- Record the actual return date, mileage, and consumed mileage.
- Log final vehicle check notes and any additional charges.
- Update the original transaction with actual rental days, total due, remaining amount, and refund if applicable.

---

## 3. Database Design

### Step 1: Analyzing the Requirements

The database will include the following entities:
- Customers
- Vehicles
- VehicleCategories
- FuelTypes
- RentalBooking
- RentalTransaction
- VehicleReturns
- Maintenance

### Step 2: Identifying Relationships

- Customers and RentalBooking: One-to-Many
- Vehicles and RentalBooking: One-to-Many
- Vehicles and Maintenance: One-to-Many
- Vehicles and FuelTypes: Many-to-One
- VehicleReturns and RentalTransaction: One-to-One

### Step 3: Designing ERD

The ERD diagram visually represents the relationships between entities (see attached diagram).

### Step 4: Designing the Relational Schema

- Customers(CustomerID, Name, ContactInformation, DriverLicenseNumber)
- Vehicles(VehicleID, Make, Model, Year, Mileage, FuelTypeID, PlateNumber, CategoryID, RentalPricePerDay, IsAvailableForRent)
- FuelTypes(ID, FuelType)
- VehicleCategories(CategoryID, CategoryName)
- RentalBooking(BookingID, CustomerID, VehicleID, RentalStartDate, RentalEndDate, PickupLocation, DropoffLocation, InitialRentalDays, RentalPricePerDay, InitialTotalDueAmount, InitialCheckNotes)
- RentalTransaction(TransactionID, BookingID, ReturnID, PaymentDetails, PaidInitialTotalDueAmount, ActualTotalDueAmount, TotalRemaining, TotalRefundedAmount, TransactionDate, UpdatedTransactionDate)
- VehicleReturns(ReturnID, ActualReturnDate, ActualRentalDays, Mileage, ConsumedMileage, FinalCheckNotes, AdditionalCharges, ActualTotalDueAmount)
- Maintenance(MaintenanceID, VehicleID, Description, MaintenanceDate, Cost)

### Step 5: Writing SQL Code

```sql
-- Customer Management
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY IDENTITY(1,1),
    Name NVARCHAR(100),
    ContactInformation NVARCHAR(100),
    DriverLicenseNumber NVARCHAR(20)
);

-- Fuel Types
CREATE TABLE FuelTypes (
    ID INT PRIMARY KEY IDENTITY(1,1),
    FuelType NVARCHAR(20)
);

-- Vehicle Categories
CREATE TABLE VehicleCategories (
    CategoryID INT PRIMARY KEY IDENTITY(1,1),
    CategoryName NVARCHAR(50)
);

-- Vehicles Information
CREATE TABLE Vehicles (
    VehicleID INT PRIMARY KEY IDENTITY(1,1),
    Make NVARCHAR(50),
    Model NVARCHAR(50),
    Year INT,
    Mileage INT,
    FuelTypeID INT,
    PlateNumber NVARCHAR(20),
    CategoryID INT,
    RentalPricePerDay DECIMAL(10, 2),
    IsAvailableForRent BIT
);

-- Vehicle Booking
CREATE TABLE RentalBooking (
    BookingID INT PRIMARY KEY IDENTITY(1,1),
    CustomerID INT,
    VehicleID INT,
    RentalStartDate DATE,
    RentalEndDate DATE,
    PickupLocation NVARCHAR(100),
    DropoffLocation NVARCHAR(100),
    InitialRentalDays TINYINT,
    RentalPricePerDay SMALLMONEY,
    InitialTotalDueAmount SMALLMONEY,
    InitialCheckNotes NVARCHAR(500)
);

-- Rental Transactions
CREATE TABLE RentalTransaction (
    TransactionID INT PRIMARY KEY IDENTITY(1,1),
    BookingID INT,
    ReturnID INT,
    PaymentDetails NVARCHAR(100),
    PaidInitialTotalDueAmount SMALLMONEY,
    ActualTotalDueAmount SMALLMONEY,
    TotalRemaining SMALLMONEY,
    TotalRefundedAmount SMALLMONEY,
    TransactionDate DATETIME,
    UpdatedTransactionDate DATETIME
);

-- Vehicle Returns
CREATE TABLE VehicleReturns (
    ReturnID INT PRIMARY KEY IDENTITY(1,1),
    ActualReturnDate DATETIME,
    ActualRentalDays TINYINT,
    Mileage INT,
    ConsumedMileage INT,
    FinalCheckNotes NVARCHAR(500),
    AdditionalCharges SMALLMONEY,
    ActualTotalDueAmount SMALLMONEY
);

-- Maintenance Records
CREATE TABLE Maintenance (
    MaintenanceID INT PRIMARY KEY IDENTITY(1,1),
    VehicleID INT,
    Description NVARCHAR(300),
    MaintenanceDate DATE,
    Cost DECIMAL(10, 2)
);
-- Add Foreign Keys to Vehicles
ALTER TABLE Vehicles
ADD CONSTRAINT FK_Vehicles_FuelTypeID
FOREIGN KEY (FuelTypeID) REFERENCES FuelTypes(ID),
ADD CONSTRAINT FK_Vehicles_CategoryID
FOREIGN KEY (CategoryID) REFERENCES VehicleCategories(CategoryID);

-- Add Foreign Keys to RentalBooking
ALTER TABLE RentalBooking
ADD CONSTRAINT FK_RentalBooking_CustomerID
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
ADD CONSTRAINT FK_RentalBooking_VehicleID
FOREIGN KEY (VehicleID) REFERENCES Vehicles(VehicleID);

-- Add Foreign Keys to RentalTransaction
ALTER TABLE RentalTransaction
ADD CONSTRAINT FK_RentalTransaction_BookingID
FOREIGN KEY (BookingID) REFERENCES RentalBooking(BookingID),
ADD CONSTRAINT FK_RentalTransaction_ReturnID
FOREIGN KEY (ReturnID) REFERENCES VehicleReturns(ReturnID);

-- Add Foreign Keys to Maintenance
ALTER TABLE Maintenance
ADD CONSTRAINT FK_Maintenance_VehicleID
FOREIGN KEY (VehicleID) REFERENCES Vehicles(VehicleID);

-- Add Foreign Keys to VehicleReturns
ALTER TABLE VehicleReturns
ADD CONSTRAINT FK_VehicleReturns_VehicleID
FOREIGN KEY (ReturnID) REFERENCES Vehicles(VehicleID);

```