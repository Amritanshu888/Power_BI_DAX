# VertiPaq Storage Engine

## 1. Introduction to VertiPaq Storage Engine

In the previous lecture, we learned about **DAX Query Evaluation** and the role of the **Storage Engine**.

The Storage Engine can be broadly divided into different types, and this lecture focuses on:

### **VertiPaq Storage Engine**

VertiPaq is the storage engine used when data is loaded into Power BI using **Import mode**.

Its storage and processing techniques are important because they help Power BI:

* Store data efficiently.
* Perform DAX calculations efficiently.
* Retrieve data faster.
* Render visuals faster.
* Improve overall report performance.

---

# 2. Connectivity Modes in Power BI

To understand when VertiPaq is used, let's first understand Power BI's connectivity modes.

Suppose you want to connect Power BI to a **Microsoft SQL Server** database.

You can go to:

```text
Power BI Desktop
      ↓
Get Data
      ↓
SQL Server
```

For SQL Server, Power BI provides two important connectivity modes:

1. **Import**
2. **DirectQuery**

> Note: These connectivity modes are not necessarily available for every possible data source in exactly the same way, but SQL Server supports both Import and DirectQuery.

---

# 3. Import Mode

In **Import mode**, Power BI creates a copy of the required source data inside the Power BI model.

The basic flow is:

```text id="3c9a5f"
SQL Server
    ↓
Import Data
    ↓
Copy of Data
    ↓
Power BI Model
    ↓
DAX Calculations
    ↓
Report / Visuals
```

### What happens?

When you import data:

1. Power BI retrieves the data from the source.
2. The data is loaded into the Power BI model.
3. Power BI stores the data in its internal storage format.
4. DAX calculations can then be performed on this stored data.
5. Reports and visuals are created using this data.

### Important

This means that when you interact with a visual, Power BI generally does **not need to query the original SQL Server database for every calculation**.

The required data is already available in the Power BI model.

---

# 4. DirectQuery Mode

In **DirectQuery mode**, Power BI does **not create a complete imported copy of the source data in the Power BI model**.

Instead, when a visual requires data, Power BI sends queries back to the source database.

The basic flow is:

```text id="u4z7o3"
Power BI Visual
      ↓
Query
      ↓
SQL Server
      ↓
Data
      ↓
Power BI Visual
```

For example, if you create a visual showing total sales, Power BI may send an appropriate query to SQL Server to retrieve the required result.

### Important Difference

In DirectQuery:

> The data remains primarily in the source system, and Power BI queries the source when data is required.

---

# 5. Import vs DirectQuery

| Feature                                | Import Mode              | DirectQuery Mode                                           |
| -------------------------------------- | ------------------------ | ---------------------------------------------------------- |
| Data copied into Power BI model        | ✅ Yes                    | ❌ Not as a full imported copy                              |
| Data primarily stored                  | Power BI model           | Source database                                            |
| Query source during visual interaction | Generally Power BI model | Source database                                            |
| VertiPaq involved                      | ✅ Yes                    | Not as the primary storage mode                            |
| Data freshness                         | Depends on refresh       | Can query more directly from source                        |
| Performance                            | Often very fast          | Depends significantly on source/database/query performance |

### Easy way to remember:

**Import:**

> Bring the data **into Power BI**.

**DirectQuery:**

> Keep the data **at the source and query it when needed**.

---

# 6. When Does VertiPaq Come Into Picture?

This is one of the most important points from the lecture.

### VertiPaq is associated with Import mode.

When you import data into Power BI, the data is stored using the VertiPaq storage engine.

```text id="8s7h6a"
Import Mode
     ↓
Power BI Data Model
     ↓
VertiPaq Storage Engine
```

Therefore, when DAX calculations are performed against imported data, the VertiPaq engine plays an important role in retrieving and processing the required data.

---

# 7. How Traditional Databases Store Data

Consider a table containing:

| Product | City      | Sales |
| ------- | --------- | ----: |
| A       | Delhi     |   100 |
| B       | Mumbai    |   200 |
| A       | Bangalore |   150 |
| C       | Delhi     |   300 |
| A       | Mumbai    |   250 |

In traditional relational databases such as:

* Microsoft SQL Server
* PostgreSQL
* MySQL

data is conceptually organized and accessed **row by row**.

For example:

```text id="8n8k3r"
Row 1 → A | Delhi     | 100
Row 2 → B | Mumbai    | 200
Row 3 → A | Bangalore | 150
Row 4 → C | Delhi     | 300
Row 5 → A | Mumbai    | 250
```

Each row contains values from all the columns.

---

# 8. VertiPaq Uses Columnar Storage

One of the most important characteristics of VertiPaq is:

### **Columnar Storage**

Instead of organizing the data primarily around complete rows, VertiPaq stores data in a **column-oriented format**.

For the same table:

### Product Column

```text id="h0m2u1"
A
B
A
C
A
```

### City Column

```text id="rj8p3e"
Delhi
Mumbai
Bangalore
Delhi
Mumbai
```

### Sales Column

```text id="j5l3qp"
100
200
150
300
250
```

Conceptually:

```text id="8m6v5e"
Product      City          Sales
   ↓           ↓             ↓
Column       Column        Column
Storage      Storage       Storage
```

This is known as:

# **Columnar Storage**

---

# 9. Why Does VertiPaq Use Columnar Storage?

Columnar storage is particularly useful for **analytical workloads** such as Power BI.

A major reason is that DAX calculations frequently need to work with **specific columns** rather than complete rows.

For example:

```DAX id="yq0x0m"
SUM(Sales[Sales])
```

If you want to calculate the total sales, you only need the:

```text
Sales column
```

You don't necessarily need:

```text
Product
City
```

for the calculation.

VertiPaq can efficiently work with the required column.

---

# 10. Vertical Scanning

The lecture highlights an important concept:

### **Vertical Scanning**

DAX calculations frequently operate on columns.

Therefore, VertiPaq can scan the relevant column directly.

For example:

```text id="6v4x4u"
Sales

100
200
150
300
250
 ↓
Scan Sales column
 ↓
100 + 200 + 150 + 300 + 250
 ↓
1000
```

Instead of processing the complete table row by row, the engine can focus on the relevant column.

This contributes to efficient analytical processing.

---

# 11. Example — Calculating Total Sales

Suppose we want:

```DAX id="xqu1y8"
Total Sales = SUM(Sales[Sales])
```

### Traditional row-oriented approach

Conceptually, the engine would process the sales values along with each row:

```text id="07oqkv"
Row 1 → Sales = 100
Row 2 → Sales = 200
Row 3 → Sales = 150
Row 4 → Sales = 300
Row 5 → Sales = 250
```

Then calculate:

```text
100 + 200 + 150 + 300 + 250
= 1000
```

### VertiPaq approach

VertiPaq can directly work with the:

```text
Sales column
```

and scan the values:

```text id="f0y4wt"
Sales Column

100
200
150
300
250
  ↓
SUM
  ↓
1000
```

This column-oriented approach is well suited to analytical calculations.

---

# 12. Example — Filter + Aggregation

Now let's make the calculation slightly more complicated.

Suppose we want:

> **Total Sales where Product = A**

Our table is:

| Product | City      | Sales |
| ------- | --------- | ----: |
| A       | Delhi     |   100 |
| B       | Mumbai    |   200 |
| A       | Bangalore |   150 |
| C       | Delhi     |   300 |
| A       | Mumbai    |   250 |

We want:

```text
Product = A
```

---

## Step 1 — Identify Matching Rows

VertiPaq identifies the rows corresponding to:

```text
Product = A
```

These are:

```text
Row 1 → A → Sales 100
Row 3 → A → Sales 150
Row 5 → A → Sales 250
```

---

## Step 2 — Use Corresponding Sales Values

The corresponding sales values are:

```text
100
150
250
```

---

## Step 3 — Calculate the Sum

```text id="b4s7fz"
100 + 150 + 250
= 500
```

So the result is:

### **Total Sales for Product A = 500**

The lecture explains this conceptually as VertiPaq finding the relevant row numbers for the filter and then evaluating the corresponding values from the Sales column.

---

# 13. Why This Improves Performance

VertiPaq's columnar storage is particularly effective for analytical workloads because DAX calculations often involve operations such as:

```text
SUM
COUNT
AVERAGE
MIN
MAX
FILTER
GROUP BY / aggregation
```

Many of these calculations can operate on selected columns.

Therefore:

```text id="5u8p1m"
Columnar Storage
       ↓
Efficient Column Access
       ↓
Efficient DAX Calculations
       ↓
Faster Data Retrieval
       ↓
Faster Visual Rendering
       ↓
Better Report Performance
```

---

# 14. VertiPaq and Report Performance

The lecture emphasizes that VertiPaq contributes to:

### 1. Efficient Storage

Data is stored using techniques designed for analytical workloads.

### 2. Faster Calculations

DAX operations can efficiently work with the relevant columns.

### 3. Faster Visual Rendering

When calculations are completed faster, Power BI visuals can be rendered faster.

### 4. Better Overall Report Performance

Therefore, efficient storage and processing ultimately contribute to better Power BI report performance.

---

# 15. Important Concept — Analytical vs Transactional Workloads

The reason columnar storage is useful becomes clearer when we compare analytical and transactional workloads.

### Transactional Workload

Usually involves operations such as:

```text
Insert one row
Update one row
Retrieve a specific record
```

Row-oriented storage can be useful for these types of operations.

### Analytical Workload

Often involves:

```text
SUM millions of sales
Calculate average revenue
Filter a large dataset
Group data by product
Calculate yearly totals
```

These operations frequently process large amounts of data from specific columns.

Therefore, **columnar storage is highly suitable for analytical workloads such as Power BI reporting.**

---

# 16. Import Mode + VertiPaq + DAX

It's useful to connect this lecture with the previous lecture about DAX Query Evaluation.

The overall picture is:

```text id="4j8v8m"
              Power BI
                  │
            Import Data
                  ↓
        ┌──────────────────┐
        │ VertiPaq Storage │
        │      Engine      │
        └──────────────────┘
                  │
           Stores Data
          Column-Oriented
                  │
                  ↓
             DAX Query
                  │
                  ↓
          Formula Engine
                  │
                  ↓
          Storage Engine
                  │
                  ↓
        Retrieve Required Data
                  │
                  ↓
             Data Cache
                  │
                  ↓
          Formula Engine
                  │
                  ↓
            Final Result
                  │
                  ↓
              Visual
```

This connects the concepts from the previous lectures.

---

# 17. Key Terms

| Term                  | Meaning                                                                                     |
| --------------------- | ------------------------------------------------------------------------------------------- |
| **VertiPaq**          | Power BI's columnar in-memory storage engine used for imported data                         |
| **Import Mode**       | Data is loaded into the Power BI model                                                      |
| **DirectQuery**       | Power BI queries the source when data is needed rather than fully importing the source data |
| **Columnar Storage**  | Data is organized/stored by columns                                                         |
| **Vertical Scanning** | Processing values down a column                                                             |
| **Data Cache**        | Temporary in-memory data returned during query processing                                   |
| **DAX**               | Language used for calculations and queries in Power BI                                      |
| **Storage Engine**    | Engine responsible for retrieving/processing stored data                                    |

---

# 18. Most Important Points for Exam/Interview

### ⭐ Point 1

**VertiPaq is a storage engine associated with Power BI's Import mode.**

### ⭐ Point 2

VertiPaq uses **columnar storage**.

### ⭐ Point 3

Columnar storage is highly suitable for **analytical workloads**.

### ⭐ Point 4

DAX calculations frequently operate on columns, making columnar storage efficient.

### ⭐ Point 5

VertiPaq can efficiently scan the required columns instead of unnecessarily processing unrelated columns.

### ⭐ Point 6

Efficient storage and processing contribute to:

**Faster calculations → Faster visuals → Better report performance**

### ⭐ Point 7

With **Import mode**, a copy of the required source data is loaded into the Power BI model.

### ⭐ Point 8

With **DirectQuery**, the data remains primarily at the source and Power BI queries the source when required.

---

# ⭐ Quick Revision

Remember this chain:

```text id="r8v4x1"
IMPORT MODE
     ↓
VERTIPAQ
     ↓
COLUMNAR STORAGE
     ↓
VERTICAL SCANNING
     ↓
EFFICIENT DAX CALCULATIONS
     ↓
FASTER VISUALS
     ↓
BETTER REPORT PERFORMANCE
```

And remember the fundamental difference:

```text id="3v5z2q"
Import Mode
→ Data is loaded into Power BI
→ VertiPaq is used
→ DAX works against the imported model

DirectQuery
→ Data remains at the source
→ Queries are sent to the source when required
```

### One-line summary:

> **VertiPaq is Power BI's columnar, in-memory storage engine used with Import mode, designed to store and scan data efficiently for analytical/DAX workloads, thereby helping calculations and report visuals execute faster.**

The lecture also mentions that **VertiPaq uses several additional techniques for efficient storage**, which will be covered in the upcoming sessions.

Absolutely. Here are **detailed, structured notes** from this lecture, covering all the important concepts and examples from the transcript.

# VertiPaq Encoding Techniques 

## 1. Introduction

In the previous lecture, we discussed the **VertiPaq Storage Engine** and learned that VertiPaq uses **columnar storage** to store data efficiently.

In this lecture, we go one step further and learn about the **encoding techniques** used by VertiPaq.

### Why does VertiPaq use encoding?

The main purpose of encoding is to:

* Reduce the amount of storage required.
* Compress the data.
* Reduce the number of bits needed to represent values.
* Make data storage more efficient.
* Ultimately improve query and report performance.

The lecture discusses three important encoding techniques:

1. **Value Encoding**
2. **Hash / Dictionary Encoding**
3. **Run-Length Encoding**

---

# 2. Value Encoding

## What is Value Encoding?

**Value encoding** is primarily useful for **numeric columns**.

The basic idea is to replace large numerical values with **smaller differences from the minimum value**.

This allows VertiPaq to store the encoded values using fewer bits.

---

## Example

Suppose we have a numerical column:

| Original Value |
| -------------: |
|            100 |
|            101 |
|            102 |
|            100 |
|            101 |

The minimum value is:

```text
Minimum = 100
```

VertiPaq can subtract this minimum value from every value.

### Encoding process

```text
Original Value - Minimum Value
```

Therefore:

| Original | Minimum | Encoded Value |
| -------: | ------: | ------------: |
|      100 |     100 |             0 |
|      101 |     100 |             1 |
|      102 |     100 |             2 |
|      100 |     100 |             0 |
|      101 |     100 |             1 |

So instead of storing:

```text
100, 101, 102, 100, 101
```

the encoded representation becomes:

```text
0, 1, 2, 0, 1
```

---

## Why is this useful?

Consider the difference between storing:

```text
100
101
102
```

and:

```text
0
1
2
```

The smaller numbers require fewer bits to represent.

Therefore:

```text
Large Values
     ↓
Subtract Minimum
     ↓
Small Differences
     ↓
Fewer Bits Required
     ↓
Less Storage
```

This is why **value encoding helps achieve compression**.

---

# 3. Important Concept Behind Value Encoding

The important idea is **not simply replacing numbers with 0, 1, 2**.

Instead:

> VertiPaq calculates a suitable base/minimum value and stores the difference between each value and that base.

For example:

```text
Original data:

100
101
102
```

Minimum:

```text
100
```

Encoded:

```text
0
1
2
```

If the original values were much larger, such as:

```text
100001
100002
100003
```

the same principle could potentially represent them as:

```text
0
1
2
```

This can significantly reduce the storage requirement when the values are close together.

---

# 4. Dictionary / Hash Encoding

The second technique discussed is:

### **Dictionary Encoding**

The lecture also refers to this as **Hash Encoding**.

This technique is particularly useful for **textual/categorical data**.

For example, suppose we have a City column:

| City     |
| -------- |
| Mumbai   |
| Delhi    |
| Calcutta |
| Mumbai   |
| Delhi    |
| Mumbai   |

There are only three unique values:

```text
Mumbai
Delhi
Calcutta
```

Instead of repeatedly storing the complete text values, VertiPaq can create a dictionary.

---

# 5. How Dictionary Encoding Works

VertiPaq assigns a numerical ID to each unique value.

For example:

| City     | Encoded ID |
| -------- | ---------: |
| Mumbai   |          1 |
| Delhi    |          2 |
| Calcutta |          3 |

The original data:

```text
Mumbai
Delhi
Calcutta
Mumbai
Delhi
Mumbai
```

can then be represented as:

```text
1
2
3
1
2
1
```

The dictionary maintains the mapping:

```text
1 → Mumbai
2 → Delhi
3 → Calcutta
```

---

# 6. Why Dictionary Encoding Saves Space

Compare the two representations.

### Original:

```text
Mumbai
Delhi
Calcutta
Mumbai
Delhi
Mumbai
```

### Encoded:

```text
1
2
3
1
2
1
```

The numerical IDs are much smaller and can be stored efficiently.

Instead of repeatedly storing strings such as:

```text
"Mumbai"
"Delhi"
"Calcutta"
```

the engine stores compact numerical references.

The dictionary retains the actual values.

Conceptually:

```text
              Dictionary
           ┌───────────────┐
           │ 1 → Mumbai    │
           │ 2 → Delhi     │
           │ 3 → Calcutta  │
           └───────────────┘
                  ↑
                  │
              Encoded Data
                  │
             1, 2, 3, 1...
```

---

# 7. When is Dictionary Encoding Useful?

Dictionary encoding is especially effective when a column contains:

* Text values.
* Categories.
* Repeated values.
* A relatively small number of unique values compared with the total number of rows.

For example:

```text
City
Gender
Country
Product Category
Department
```

If millions of rows contain only a few thousand unique values, dictionary encoding can greatly reduce the amount of repeated information that needs to be stored.

---

# 8. Run-Length Encoding (RLE)

The third technique discussed is:

# **Run-Length Encoding**

Run-Length Encoding, commonly abbreviated as **RLE**, is particularly useful when the same value appears **consecutively**.

The key idea is:

> Instead of storing the same value repeatedly, store the value once along with the number of consecutive times it occurs.

---

# 9. Example of Run-Length Encoding

Suppose our original City column is:

| Row | City     |
| --: | -------- |
|   1 | Mumbai   |
|   2 | Mumbai   |
|   3 | Mumbai   |
|   4 | Mumbai   |
|   5 | Delhi    |
|   6 | Delhi    |
|   7 | Calcutta |
|   8 | Calcutta |

Notice the consecutive repetitions:

```text
Mumbai   → 4 times
Delhi    → 2 times
Calcutta → 2 times
```

Instead of storing:

```text
Mumbai
Mumbai
Mumbai
Mumbai
Delhi
Delhi
Calcutta
Calcutta
```

we can represent it as:

```text
Mumbai    → 4
Delhi     → 2
Calcutta  → 2
```

Or conceptually:

```text
(Value, Count)

(Mumbai, 4)
(Delhi, 2)
(Calcutta, 2)
```

---

# 10. Why Run-Length Encoding Saves Space

Without RLE:

```text
Mumbai
Mumbai
Mumbai
Mumbai
```

The value is stored repeatedly.

With RLE:

```text
Mumbai → 4
```

The repeated value is represented once along with its count.

Therefore:

```text
Repeated Values
      ↓
Identify Consecutive Runs
      ↓
Store Value + Count
      ↓
Less Storage
```

---

# 11. Importance of Data Ordering for RLE

A key idea behind Run-Length Encoding is **consecutive repetition**.

For example:

### Very suitable for RLE

```text
Mumbai
Mumbai
Mumbai
Mumbai
Delhi
Delhi
Delhi
```

There are long consecutive runs.

### Less suitable for RLE

```text
Mumbai
Delhi
Mumbai
Delhi
Mumbai
Delhi
```

Even though Mumbai and Delhi repeat, they aren't consecutive.

Therefore, RLE benefits significantly when similar values are grouped together.

---

# 12. Comparison of Encoding Techniques

| Encoding Technique             | Mainly Useful For           | Basic Idea                                      |
| ------------------------------ | --------------------------- | ----------------------------------------------- |
| **Value Encoding**             | Numeric data                | Store differences from a base/minimum value     |
| **Dictionary / Hash Encoding** | Text/categorical data       | Replace unique values with numerical IDs        |
| **Run-Length Encoding**        | Repeated consecutive values | Store value + number of consecutive occurrences |

---

# 13. Value Encoding vs Dictionary Encoding

These two can sometimes sound similar because both produce smaller numerical representations, but the underlying idea is different.

### Value Encoding

Works with **numeric values**.

Example:

```text
100 → 0
101 → 1
102 → 2
```

based on subtracting the minimum/base.

### Dictionary Encoding

Works by assigning an ID to each **unique value**.

Example:

```text
Mumbai → 1
Delhi → 2
Calcutta → 3
```

So:

```text
Value Encoding
→ Difference from base

Dictionary Encoding
→ ID representing unique value
```

---

# 14. Can VertiPaq Use Multiple Encoding Techniques?

### Yes.

This is an important point from the lecture.

The examples in the lecture are shown separately to explain each technique.

However, in an actual Power BI data model:

> **VertiPaq can use multiple encoding/compression techniques together to store data efficiently.**

It isn't necessarily restricted to choosing only one technique for the entire model.

The goal is always:

```text
Efficient Encoding
       ↓
Better Compression
       ↓
Less Memory Usage
       ↓
Efficient Data Processing
       ↓
Better Report Performance
```

---

# 15. Putting Everything Together

Let's connect this lecture with the previous VertiPaq lecture.

### Step 1 — Data is imported

```text
SQL Server / Other Source
          ↓
     Import Mode
          ↓
      Power BI
```

### Step 2 — VertiPaq stores the data

```text
Power BI Model
      ↓
VertiPaq
      ↓
Columnar Storage
```

### Step 3 — VertiPaq applies compression/encoding

Depending on the characteristics of the data, techniques such as:

```text
Value Encoding
Dictionary Encoding
Run-Length Encoding
```

can be used.

### Step 4 — DAX queries the data

```text
DAX Query
    ↓
Formula Engine
    ↓
Storage Engine
    ↓
VertiPaq
    ↓
Compressed Data
    ↓
Required Data
    ↓
Formula Engine
    ↓
Final Result
```

---

# 16. Overall Benefits of Encoding

Encoding techniques help VertiPaq achieve:

### 1. Reduced Storage

Smaller representations require less memory.

### 2. Better Compression

Repeated or large values can be represented more efficiently.

### 3. Faster Data Access

The engine can work with compact encoded data.

### 4. Better Memory Utilization

More data can fit into available memory.

### 5. Better Report Performance

Efficient storage and processing ultimately help Power BI reports and visuals perform faster.

---

# 17. Real-World Example

Imagine a Power BI sales dataset with:

**10 million rows**

and columns such as:

```text
Product
City
Sales
```

Suppose:

### City

There are 10 million rows but only:

```text
50 unique cities
```

Dictionary encoding can represent those 50 cities using compact IDs.

### Sales

Suppose sales values are within a relatively narrow numerical range.

Value encoding can represent them efficiently relative to a base value.

### City after sorting

If the data contains:

```text
Delhi
Delhi
Delhi
Delhi
Mumbai
Mumbai
Mumbai
Mumbai
Mumbai
```

Run-Length Encoding can efficiently represent the repeated runs.

So VertiPaq can combine different techniques to achieve efficient storage.

---

# 18. ⭐ Important Points for Interview/Exam

### Value Encoding

> Used primarily for numerical data by storing differences relative to a base/minimum value, reducing the number of bits required.

### Dictionary Encoding

> Assigns a numerical ID to each unique value and stores those IDs instead of repeatedly storing the original values.

### Run-Length Encoding

> Compresses consecutive repeated values by storing the value and the number of consecutive occurrences.

### Multiple Techniques

> VertiPaq can use multiple encoding/compression techniques together depending on the characteristics of the data.

---

# ⭐ Quick Revision

Remember the three techniques like this:

```text
1. VALUE ENCODING
   ↓
   Numeric values
   ↓
   Subtract minimum/base
   ↓
   Smaller numbers
   ↓
   Less storage


2. DICTIONARY ENCODING
   ↓
   Text / categorical values
   ↓
   Unique values → IDs
   ↓
   Store IDs
   ↓
   Less storage


3. RUN-LENGTH ENCODING
   ↓
   Consecutive repetitions
   ↓
   Value + Count
   ↓
   Less storage
```

### One-line summary:

> **VertiPaq uses encoding techniques such as Value Encoding, Dictionary Encoding, and Run-Length Encoding to represent data more compactly, reduce memory consumption, and improve the efficiency and performance of Power BI reports.**

And the most important overall concept from this lecture is:

**VertiPaq doesn't simply store the raw data—it uses intelligent encoding and compression techniques to store the data in a much more memory-efficient form.**



## Difference between Measure and Column

| Aspect | Calculated Columns | Measures |
|---|---|---|
| Evaluation Time | Calculated at data refresh and stored in the model | Calculated at query time when used in visuals |
| Storage | Stored in the data model, consumes memory and disk | Not stored as data; only calculated on demand |
| Context | Evaluated row-by-row (row context) | Evaluated in filter context (affected by slicers, filters, visuals) |
| Usage | Adds new columns to tables; can be used in slicers and filters | Used for aggregations and calculations across data; cannot be used as slicers or filters |
| Performance Impact | Increases model size; can slow refresh time | No impact on model size; may affect query speed depending on complexity |
| Examples | Create a new column: `DiscountPrice = Price * 0.9` | Calculate sum: `TotalSales = SUM(Sales[Amount])` |
| When to use | When row-by-row calculation is needed or to create new column data | For aggregation and calculations that depend on user interaction/filtering |