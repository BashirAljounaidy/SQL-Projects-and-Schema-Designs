# 📊 Karate Club SQL Project

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

This project aims to develop a database management system for a Karate Club. The system will manage membership, instructor profiles, belt rank progression, testing, and payments. The primary goal is to automate member enrollment, fee management, belt rank tracking, and instructor assignments.

### Objectives

- Efficiently manage member profiles and their subscription periods.
- Track belt rank progression and testing results.
- Manage instructor profiles and assignments to members.
- Handle payment and fee management, including membership and test fees.

---

## 2. Requirements

### Membership Management:
- Create and manage member profiles (Name, Address, ContactInfo, Emergency Contact).
- Track membership start and end dates, fees, and payment status.
- Allow membership enrollment, renewal, and updates.
- Maintain membership status (active/inactive).

### Instructor Management:
- Create and manage instructor profiles (Name, Address, ContactInfo, Qualifications).
- Track instructor assignments to members.
- Allow multiple instructors to train one member and vice versa.

### Belt Rank and Testing:
- Manage belt rank tests and progression.
- Track belt test dates, results, and responsible instructors.
- Store the current belt rank of each member.
- Fixed belt ranks (17 levels from White Belt to Black Belt 10th Dan).
- Record belt test fees.

### Payment and Fee Management:
- Manage membership and test fees.
- Track payments, including amount, date, and status.
- Maintain a payment history for each member.

---

## 3. Database Design

### Step 1: Analyzing the Requirements

The database will include the following entities:
- Members
- Instructors
- Belt Ranks
- Belt Tests
- Payments
- Subscription Periods

### Step 2: Identifying Relationships

- Members and Instructors: Many-to-Many
- Members and Belt Tests: One-to-Many
- Members and Payments: One-to-Many
- Belt Ranks and Belt Tests: One-to-Many

### Step 3: Designing ERD

The ERD diagram visually represents the relationships between entities (see attached diagram).

### Step 4: Designing the Relational Schema

- Members(MemberID, PersonID, EmergencyContactInfo, LastBeltRank, IsActive)
- Persons(PersonID, Name, Address, ContactInfo)
- Instructors(InstructorID, PersonID, Qualification)
- MemberInstructors(MemberID, InstructorID, AssignDate)
- BeltRanks(RankID, RankName, TestFees)
- BeltTests(TestID, MemberID, RankID, Result, Date, TestedByInstructorID, PaymentID)
- Payments(PaymentID, Amount, Date, MemberID)
- SubscriptionPeriods(PeriodID, StartDate, EndDate, Fees, MemberID, PaymentID)

### Step 5: Writing SQL Code

```sql
-- Members Management
CREATE TABLE Persons (
    PersonID INT PRIMARY KEY IDENTITY(1,1),
    Name NVARCHAR(100),
    Address NVARCHAR(100),
    ContactInfo NVARCHAR(100)
);

CREATE TABLE Members (
    MemberID INT PRIMARY KEY IDENTITY(1,1),
    PersonID INT,
    EmergencyContactInfo NVARCHAR(100),
    LastBeltRank INT,
    IsActive BIT
);

-- Instructors Management
CREATE TABLE Instructors (
    InstructorID INT PRIMARY KEY IDENTITY(1,1),
    PersonID INT,
    Qualification NVARCHAR(100)
);

CREATE TABLE MemberInstructors (
    MemberID INT,
    InstructorID INT,
    AssignDate DATETIME,
    PRIMARY KEY (MemberID, InstructorID)
);

-- Belt Ranks
CREATE TABLE BeltRanks (
    RankID INT PRIMARY KEY IDENTITY(1,1),
    RankName NVARCHAR(50),
    TestFees SMALLMONEY
);

-- Belt Tests
CREATE TABLE BeltTests (
    TestID INT PRIMARY KEY IDENTITY(1,1),
    MemberID INT,
    RankID INT,
    Result BIT,
    Date DATE,
    TestedByInstructorID INT,
    PaymentID INT
);

-- Payment Management
CREATE TABLE Payments (
    PaymentID INT PRIMARY KEY IDENTITY(1,1),
    Amount DECIMAL(10, 2),
    Date DATETIME,
    MemberID INT
);

-- Subscription Periods
CREATE TABLE SubscriptionPeriods (
    PeriodID INT PRIMARY KEY IDENTITY(1,1),
    StartDate DATETIME,
    EndDate DATETIME,
    Fees SMALLMONEY,
    MemberID INT,
    PaymentID INT
);
-- Adding Foreign Keys to Members
ALTER TABLE Members
ADD CONSTRAINT FK_Members_PersonID
FOREIGN KEY (PersonID) REFERENCES Persons(PersonID),
ADD CONSTRAINT FK_Members_LastBeltRank
FOREIGN KEY (LastBeltRank) REFERENCES BeltRanks(RankID);

-- Adding Foreign Keys to Instructors
ALTER TABLE Instructors
ADD CONSTRAINT FK_Instructors_PersonID
FOREIGN KEY (PersonID) REFERENCES Persons(PersonID);

-- Adding Foreign Keys to MemberInstructors
ALTER TABLE MemberInstructors
ADD CONSTRAINT FK_MemberInstructors_MemberID
FOREIGN KEY (MemberID) REFERENCES Members(MemberID),
ADD CONSTRAINT FK_MemberInstructors_InstructorID
FOREIGN KEY (InstructorID) REFERENCES Instructors(InstructorID);

-- Adding Foreign Keys to Payments
ALTER TABLE Payments
ADD CONSTRAINT FK_Payments_MemberID
FOREIGN KEY (MemberID) REFERENCES Members(MemberID);

-- Adding Foreign Keys to BeltTests
ALTER TABLE BeltTests
ADD CONSTRAINT FK_BeltTests_MemberID
FOREIGN KEY (MemberID) REFERENCES Members(MemberID),
ADD CONSTRAINT FK_BeltTests_RankID
FOREIGN KEY (RankID) REFERENCES BeltRanks(RankID),
ADD CONSTRAINT FK_BeltTests_TestedByInstructorID
FOREIGN KEY (TestedByInstructorID) REFERENCES Instructors(InstructorID),
ADD CONSTRAINT FK_BeltTests_PaymentID
FOREIGN KEY (PaymentID) REFERENCES Payments(PaymentID);

-- Adding Foreign Keys to SubscriptionPeriods
ALTER TABLE SubscriptionPeriods
ADD CONSTRAINT FK_SubscriptionPeriods_MemberID
FOREIGN KEY (MemberID) REFERENCES Members(MemberID),
ADD CONSTRAINT FK_SubscriptionPeriods_PaymentID
FOREIGN KEY (PaymentID) REFERENCES Payments(PaymentID);

```