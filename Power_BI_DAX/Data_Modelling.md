## Data Modelling Concepts

### ⭐ Star Schema

A **Star Schema** is a **data warehouse design** where a central **Fact Table** is connected to multiple **Dimension Tables**.

```text
                 Date
                  |
                  |
Customer ---- Sales Fact ---- Product
                  |
                Store
```

### 1. Fact Table

Stores **business events and numerical measurements**.

Example:

```text
Sales_Fact
----------------
date_id
customer_id
product_id
store_id
quantity
sales_amount
profit
```

### 2. Dimension Tables

Store **descriptive information** about the facts.

Examples:

```text
Customer_Dim → customer_name, city, age
Product_Dim  → product_name, category, brand
Date_Dim     → day, month, year
Store_Dim    → store_name, city, region
```

### Simple way to remember

> **Fact = What happened?**
> **Dimension = Context about what happened.**

For example:

**₹5,000 sale** → Fact
**Who bought it?** → Customer Dimension
**What was bought?** → Product Dimension
**When?** → Date Dimension
**Where?** → Store Dimension

### Why "Star"?

Because the **Fact Table is in the center** and Dimension Tables surround it like a ⭐.

**Main purpose:** Star Schema is mainly used for **data warehousing, analytics, reporting, and BI** because it makes analytical queries simpler and efficient.

### ❄️ Snowflake Schema

A **Snowflake Schema** is a data warehouse design similar to Star Schema, but the **Dimension Tables are further normalized into multiple related tables**.

```text
                    Date
                     |
                     |
Customer ---- Sales Fact ---- Product
                              |
                              ↓
                           Category
                              |
                              ↓
                          Department
```

### Main difference from Star Schema

**Star Schema:**

```text
Fact → Product
       ├── Product Name
       ├── Category
       └── Brand
```

Everything is kept in **one dimension table**.

**Snowflake Schema:**

```text
Fact → Product → Category → Department
```

The dimension is split into **multiple tables** to reduce data duplication.

### Simple way to remember

> ⭐ **Star = Dimensions are mostly denormalized**
> ❄️ **Snowflake = Dimensions are normalized into multiple tables**

### Example

Instead of:

```text
Product_Dim
product_id
product_name
category
department
```

Snowflake might have:

```text
Product_Dim
product_id
product_name
category_id

Category_Dim
category_id
category_name
department_id

Department_Dim
department_id
department_name
```

### Key trade-off

**Snowflake → less redundancy, more joins**
**Star → more redundancy, simpler/faster queries**

So in an interview, the simplest answer is:

> **Snowflake Schema is an extension of Star Schema where dimension tables are normalized into multiple related tables.**

## Fact Table
- Data Type: Stores numeric measurable data
- Purpose: Stores numbers or measurements to analyze results
- Size: Usually large with many rows
- Relation: Links to dimension tables via foreign keys

## Dimension Table
- Data Type: Stores descriptive attributes
- Purpose: Stores details or descriptions to explain facts
- Size: Smaller with more descriptive columns
- Relation: Has primary key referenced by fact table

- In the fact table we generally have the foreign keys while in the dimension table we have the primary keys and to these primary keys we relate the fact and the dimension table
- Primary Key in the dimension table and foreign key in the fact table can be used to relate the dimension and fact table 

- Example:
- Dimensional Table: Customers
- Columns: CustomerID(Primary Key), Name, City, Age
- Here CustomerID is the Primary Key(PK) uniquely identifying each customer
- Primary key is a column or a group of columns that can be used to identify each record in a table uniquely, so u can use any CustomerID in the customers dimension table to identify any given record

- Fact Table: Sales
- Columns: SaleID(Primary Key), CustomerID(Foreign Key), Product, Quantity, Price
- The CustomerID in the Sales table is a Foreign Key(FK) linking to the CustomerID in the Customers table
- Foreign Key can have duplicate values while the Primary Key cannot have duplicate values
- Foreign key can also have null values while the primary key will never have null values
- Through CustomerID the Customers Table and Sales Table will be related to each other

## Cardinality 

## 1. What is Cardinality?

**Cardinality** defines the type of relationship that exists between two tables based on how many times a value from one table can occur in another table.

In data modeling, especially in **Power BI**, understanding cardinality is important because it determines how tables should be related to each other.

The main types of relationships discussed are:

1. **One-to-One (1:1)**
2. **One-to-Many (1:Many)**
3. **Many-to-Many (Many:Many)**

---

# 2. Example 1 — One-to-One Relationship

Suppose we have two tables:

### Table 1: Employees

| Employee ID | Employee Name |
| ----------- | ------------- |
| 1           | Alice         |
| 2           | Bob           |
| 3           | Carol         |

Here, **Employee ID** is the **Primary Key**.

### Table 2: Employee Details

| Employee ID | Address   | Salary |
| ----------- | --------- | ------ |
| 1           | Delhi     | 50000  |
| 2           | Mumbai    | 60000  |
| 3           | Bangalore | 55000  |

Here, **Employee ID** is the **Foreign Key**.

### Relationship

The two tables can be connected using the common column:

**Employee ID**

The important point is:

* Each Employee ID appears **exactly once** in Table 1.
* The same Employee ID appears **exactly once** in Table 2.

Therefore:

**One employee ↔ One employee detail record**

This is called a:

### **One-to-One (1:1) Relationship**

```text
Employees                  Employee Details

Employee ID  1  ──────────  1  Employee ID
Employee ID  2  ──────────  2  Employee ID
Employee ID  3  ──────────  3  Employee ID
```

### Key terms

**Primary Key (PK):**

* Uniquely identifies each record in a table.
* Values cannot be duplicated.

**Foreign Key (FK):**

* A column that references a key in another table.
* It is used to establish relationships between tables.

### Important idea

For a **1:1 relationship**:

> Each value in the relationship column on one side corresponds to exactly one value on the other side.

---

# 3. Example 2 — One-to-Many Relationship

Now consider two different tables:

### Table 1: Customers

| Customer ID | Customer Name |
| ----------- | ------------- |
| 1           | Alice         |
| 2           | Bob           |
| 3           | Carol         |

Here:

**Customer ID = Primary Key**

### Table 2: Orders

| Order ID | Customer ID |
| -------- | ----------- |
| 101      | 1           |
| 102      | 1           |
| 103      | 2           |
| 104      | 3           |
| 105      | 3           |

Here:

**Customer ID = Foreign Key**

---

## Understanding the relationship

Let's look at **Customer ID = 1**.

In the **Customers** table:

```text
Customer ID = 1 → occurs once
```

But in the **Orders** table:

```text
Customer ID = 1 → occurs multiple times
```

Why?

Because one customer can place multiple orders.

For example:

```text
Customer 1
    │
    ├── Order 101
    └── Order 102
```

Similarly:

```text
Customer 3
    │
    ├── Order 104
    └── Order 105
```

Therefore:

**One Customer → Many Orders**

This is called a:

### **One-to-Many (1:Many) Relationship**

---

## Which table is "One" and which is "Many"?

In this example:

**Customers = One side**

**Orders = Many side**

So we represent the relationship as:

```text
Customers             Orders

    1       ────────<      Many
```

Or:

```text
Customers (1) ───────── (Many) Orders
```

### Why?

Because:

* Customer ID occurs **once** in Customers.
* Customer ID can occur **multiple times** in Orders.

---

## Important Rule

Whenever:

> A value occurs once in Table 1 but can occur multiple times in Table 2

the relationship is:

### **One-to-Many (1:Many)**

This is one of the **most common relationships in data modeling**.

For example:

```text
Customer → Orders
Department → Employees
Product → Sales
Employee → Attendance Records
```

In all these cases, one entity can have multiple related records.

---

# 4. Example 3 — Many-to-Many Relationship

Now consider two tables:

### Table 1: Students

Suppose the `Name` column contains:

| Name  |
| ----- |
| Alice |
| Alice |
| Bob   |
| Carol |

Notice:

**Alice occurs twice.**

---

### Table 2: Student Scores

Suppose:

| Name  | Score |
| ----- | ----: |
| Alice |    85 |
| Alice |    90 |
| Bob   |    75 |
| Carol |    88 |

Again:

**Alice occurs twice.**

---

## Understanding the relationship

If we try to establish a relationship between the two tables using the **Name** column:

### In Table 1:

```text
Alice → Multiple occurrences
```

### In Table 2:

```text
Alice → Multiple occurrences
```

Therefore, the same value occurs multiple times on **both sides**.

This creates a:

# **Many-to-Many (Many:Many) Relationship**

Represented as:

```text
Students             Student Scores

   Many       ────────       Many
```

---

## What about Bob and Carol?

In the example:

* Bob occurs once in Table 1.
* Bob occurs once in Table 2.
* Carol occurs once in Table 1.
* Carol occurs once in Table 2.

However, the overall relationship is still considered **Many-to-Many** because **Alice occurs multiple times in both tables**.

### Very important point

You don't need **every single value** to occur multiple times.

If the relationship column has **at least one value that occurs multiple times on both sides**, the relationship can fall under the **Many-to-Many** category.

---

# 5. Comparison of All Three Relationships

| Relationship                 | Table 1                        | Table 2                        | Example                     |
| ---------------------------- | ------------------------------ | ------------------------------ | --------------------------- |
| **One-to-One (1:1)**         | Value occurs once              | Value occurs once              | Employee → Employee Details |
| **One-to-Many (1:Many)**     | Value occurs once              | Value can occur multiple times | Customer → Orders           |
| **Many-to-Many (Many:Many)** | Value can occur multiple times | Value can occur multiple times | Students → Student Scores   |

---

# 6. Primary Key vs Foreign Key

The lecture also demonstrates the importance of **Primary Keys and Foreign Keys**.

### Primary Key

A **Primary Key** is a column that uniquely identifies each row in a table.

Properties:

* Values are unique.
* A value cannot normally appear multiple times.
* It identifies a particular record.

Example:

```text
Customer ID
1
2
3
4
```

Each customer has a unique ID.

---

### Foreign Key

A **Foreign Key** is a column used to reference a key from another table.

Unlike a primary key, a foreign key **can contain duplicate values**.

Example:

```text
Customers

Customer ID
1
2
3
```

Orders:

```text
Customer ID
1
1
1
2
3
3
```

Here, `Customer ID` in Orders is a foreign key.

Customer 1 can have several orders, so the value `1` appears multiple times.

---

# 7. How to Identify Cardinality

A simple way to identify cardinality is to examine how frequently the relationship column occurs in each table.

### Case 1: One-to-One

```text
Table 1 → Value occurs once
Table 2 → Value occurs once
```

Therefore:

**1 : 1**

---

### Case 2: One-to-Many

```text
Table 1 → Value occurs once
Table 2 → Value occurs multiple times
```

Therefore:

**1 : Many**

---

### Case 3: Many-to-Many

```text
Table 1 → Value occurs multiple times
Table 2 → Value occurs multiple times
```

Therefore:

**Many : Many**

---

# 8. Visual Representation

### One-to-One

```text
Table A                    Table B

   1   ───────────────────   1
```

### One-to-Many

```text
Table A                    Table B

   1   ───────────────────   *
                              ├── Record
                              ├── Record
                              └── Record
```

`*` represents **many**.

### Many-to-Many

```text
Table A                    Table B

   *   ───────────────────   *
```

---

# 9. Why Cardinality Matters in Power BI

Cardinality is particularly important when creating relationships between tables in **Power BI**.

When you connect two tables using a common column, Power BI needs to know how the values are related.

The relationship can be:

* **1:1**
* **1:* (One-to-Many)**
* ***:1 (Many-to-One)**
* ***:* (Many-to-Many)**

For example:

```text
Customers (1) ───────── (*) Orders
```

This tells Power BI:

> One customer can have many orders.

Understanding this becomes important when working with **DAX functions**, filtering, aggregation, and data modeling.

---

# 10. Key Takeaways

### One-to-One

> One record in Table A is related to exactly one record in Table B.

**Example:**

```text
Employee → Employee Details
```

---

### One-to-Many

> One record in Table A can be related to multiple records in Table B.

**Example:**

```text
Customer → Orders
```

This is one of the most common relationships.

---

### Many-to-Many

> Multiple records in Table A can be related to multiple records in Table B.

**Example from the lecture:**

```text
Students ↔ Student Scores
```

where a value such as `Alice` occurs multiple times in both tables.

---

## ⭐ Quick Revision

Remember this simple pattern:

```text
Once  ↔ Once       →  1 : 1

Once  ↔ Multiple   →  1 : Many

Multiple ↔ Multiple → Many : Many
```

### The easiest way to remember:

**1 : 1**

> One person → One record

**1 : Many**

> One customer → Many orders

**Many : Many**

> Many records → Many records

The lecture concludes that these cardinality concepts will be used in upcoming Power BI sessions, particularly when discussing **relevant DAX functions and data modeling concepts**.

