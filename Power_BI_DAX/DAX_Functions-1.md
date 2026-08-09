# DAX Aggregate Functions — Detailed Lecture Notes

## 1. Overview

The previous session covered some DAX functions. In this session, the focus is primarily on **aggregate functions**.

The lecture also uses some non-aggregate functions such as:

* `FILTER()`
* `OR()`

These functions are used to demonstrate how aggregate functions can be applied to **specific subsets of data**. 

The major aggregate functions discussed are:

| Function     | Purpose                                           |
| ------------ | ------------------------------------------------- |
| `SUM()`      | Calculates the total/sum of values                |
| `SUMX()`     | Calculates a sum over a filtered/table expression |
| `MIN()`      | Finds the minimum value                           |
| `MINX()`     | Finds minimum value over a table expression       |
| `MAX()`      | Finds the maximum value                           |
| `MAXX()`     | Finds maximum value over a table expression       |
| `AVERAGE()`  | Calculates average                                |
| `AVERAGEX()` | Calculates average over a table expression        |
| `COUNT()`    | Counts values in a column                         |
| `COUNTX()`   | Counts values over a table expression             |
| `PRODUCT()`  | Calculates product of values                      |
| `PRODUCTX()` | Calculates product over a table expression        |

---

# 2. Existing Data Model

The lecture starts with an already-created **Date Table**.

The Date Table contains columns such as:

* Weekday
* Month Number
* Week Number
* Month Short Name
* Length Function column

The instructor then switches to **Report View** to demonstrate the functions. 

---

# 3. Creating a `SUM()` Measure

The first aggregate function demonstrated is `SUM()`.

### Objective

Find the **sum of values in the `Length` column**.

### Steps

1. Right-click the **Date Table**.
2. Select **New Measure**.
3. Enter the measure formula.
4. Use the `SUM()` function.
5. Select the `Length` column.
6. Press `Enter`.

Conceptually:

```DAX
Sum Function =
SUM('Date Table'[Length])
```

The instructor then places this measure inside a **Card visual** so that the calculated value can be displayed. 

### Card Visual Steps

1. Click a blank area on the report canvas.
2. Select **Card** visual.
3. Drag/select the `Sum Function` measure in the card's field area.
4. The card displays the calculated sum.

The instructor also formats the card:

**Format Visual → General → Effects → Visual Border → On**. 

---

# 4. Creating a Separate Measures Table

Initially, the `Sum Function` measure resides under the **Date Table**.

However, when a Power BI model contains many tables and many measures, keeping all measures inside individual data tables can make the model difficult to manage.

Therefore, the instructor creates a separate table called **Measures Table**.

### Why create a Measures Table?

It provides a central location for measures and makes them easier to identify and manage when the model becomes large. 

### Steps to Create Measures Table

1. Go to the **Home** tab.
2. Select **Enter Data**.
3. Don't enter any actual data.
4. Rename the table to:

```text
Measures Table
```

5. Click **Load**.

The Measures Table is created.

### Move Existing Measure to Measures Table

1. Select the `Sum Function` measure.
2. Open its properties/context options.
3. Find **Home Table**.
4. Change the Home Table from `Date Table` to `Measures Table`.

The measure now appears under the Measures Table. 

### Remove Unnecessary Column

The newly created Measures Table contains a dummy `Column 1`.

Since it isn't required:

1. Select `Column 1`.
2. Choose **Delete from Model**.

The Measures Table now contains only the measures. 

> **Best Practice:** Keep your DAX measures in a dedicated Measures Table, especially when working with larger Power BI models.

---

# 5. `SUMX()` — Sum with Filtering

Now the instructor wants to calculate:

> Sum of `Length` values **only for December**.

This is where `SUMX()` and `FILTER()` are introduced. 

### Formula Structure

Conceptually:

```DAX
Sum X Function =
SUMX(
    FILTER(
        'Date Table',
        'Date Table'[Month Short Name] = "Dec"
    ),
    'Date Table'[Length]
)
```

### How it works

First:

```DAX
FILTER('Date Table', ...)
```

creates a filtered version of the Date Table containing only December rows.

The condition is:

```DAX
'Date Table'[Month Short Name] = "Dec"
```

Then:

```DAX
SUMX(..., 'Date Table'[Length])
```

calculates the sum of the `Length` values from that filtered table. 

### Important Structure

```text
SUMX
 │
 ├── FILTER(Date Table)
 │       │
 │       └── Month Short Name = "Dec"
 │
 └── Length column
```

### Why is the result smaller?

The normal `SUM()` calculates the sum across the available values, whereas this `SUMX()` example first restricts the rows to **December**.

Therefore:

```text
SUMX result < SUM result
```

in this example. 

---

# 6. `MIN()` Function

Next, the instructor demonstrates how to find the **minimum value** of the `Length` column.

### Steps

1. Right-click **Measures Table**.
2. Select **New Measure**.
3. Name it `Min Fun`.
4. Enter the `MIN()` function.
5. Select the `Length` column.
6. Press `Enter`.

Conceptually:

```DAX
Min Fun =
MIN('Date Table'[Length])
```

The result is then displayed using a Card visual. 

---

# 7. `MINX()` — Minimum for December

Now the requirement changes:

> Find the minimum `Length` value **only for December**.

The instructor uses `MINX()` together with `FILTER()`.

### Formula

```DAX
Min X Function =
MINX(
    FILTER(
        'Date Table',
        'Date Table'[Month Short Name] = "Dec"
    ),
    'Date Table'[Length]
)
```

### Execution flow

```text
Date Table
    ↓
FILTER()
    ↓
Keep only December rows
    ↓
MINX()
    ↓
Find minimum Length
```

The result is then placed in another Card visual. 

---

# 8. `MAX()` Function

Next, the instructor calculates the **maximum value** from the `Length` column.

### Formula

```DAX
Max Fun =
MAX('Date Table'[Length])
```

### Steps

1. Right-click Measures Table.
2. Select **New Measure**.
3. Name it `Max Fun`.
4. Enter `MAX()`.
5. Select `Length`.
6. Press `Enter`.
7. Display it using a Card visual.

The card shows the maximum value from the `Length` column. 

---

# 9. `MAXX()` with Multiple Conditions

Now a slightly more interesting example is used.

### Requirement

Find the maximum value from the **Month Number** column, but only when the month is:

* January
* November

The instructor uses:

* `MAXX()`
* `FILTER()`
* `OR()`



### Formula

Conceptually:

```DAX
Max X Function =
MAXX(
    FILTER(
        'Date Table',
        OR(
            'Date Table'[Month Short Name] = "January",
            'Date Table'[Month Short Name] = "November"
        )
    ),
    'Date Table'[Month Number]
)
```

### Understanding `OR()`

The condition says:

```text
Month = January
OR
Month = November
```

So only January and November rows are retained.

Then `MAXX()` evaluates the `Month Number` column.

### Why is the answer 11?

January:

```text
Month Number = 1
```

November:

```text
Month Number = 11
```

Therefore:

```text
MAX(1, 11) = 11
```

So the Card displays **11**. 

---

# 10. `AVERAGE()` Function

The next aggregate function is `AVERAGE()`.

### Requirement

Find the average of values in the **Month Number** column.

### Formula

```DAX
Average Fun =
AVERAGE('Date Table'[Month Number])
```

### Steps

1. Right-click Measures Table.
2. Select **New Measure**.
3. Name it `Average Fun`.
4. Enter `AVERAGE()`.
5. Select `Month Number`.
6. Press `Enter`.
7. Put the measure in a Card visual.

The card displays the average of the Month Number values. 

---

# 11. `AVERAGEX()` with Filtering

Now the instructor calculates the average of `Month Number`, but only for:

* November
* December

The transcript describes using `AVERAGEX()` together with `FILTER()` and `OR()`. 

### Formula

The intended structure is:

```DAX
Average X Function =
AVERAGEX(
    FILTER(
        'Date Table',
        OR(
            'Date Table'[Month Short Name] = "November",
            'Date Table'[Month Short Name] = "December"
        )
    ),
    'Date Table'[Month Number]
)
```

### Calculation

November:

```text
Month Number = 11
```

December:

```text
Month Number = 12
```

Therefore:

```text
Average = (11 + 12) / 2
       = 11.5
```

The lecture transcript states the result as **11.51**, although mathematically `(11 + 12) / 2` is **11.5**. 

So for your notes, remember:

> **November = 11, December = 12 → average = 11.5**

---

# 12. `COUNT()` Function

Next, the instructor moves to `COUNT()` and `COUNTX()`.

### Requirement

Count the values in the **Month Short Name** column.

### Formula

```DAX
Count Function =
COUNT('Date Table'[Month Short Name])
```

### Steps

1. Right-click Measures Table.
2. Select **New Measure**.
3. Name it `Count Function`.
4. Use `COUNT()`.
5. Select `Month Short Name`.
6. Press `Enter`.
7. Display it in a Card visual.

The lecture says the result is **366** because the Date Table contains dates from **1 January 2022 to 1 January 2023**. 

---

# 13. `COUNTX()` with Filtering

Now the requirement is:

> Count values only where the month is **January OR December**.

The instructor uses:

* `COUNTX()`
* `FILTER()`
* `OR()`

### Formula Structure

```DAX
Count X Function =
COUNTX(
    FILTER(
        'Date Table',
        OR(
            'Date Table'[Month Short Name] = "January",
            'Date Table'[Month Short Name] = "December"
        )
    ),
    'Date Table'[Month Number]
)
```

### Execution

```text
Date Table
     ↓
FILTER()
     ↓
January OR December
     ↓
COUNTX()
     ↓
Count Month Number values
```

The result is displayed using another Card visual. 

The instructor also renames the measure afterward:

```text
Count X Function
```

---

# 14. `PRODUCT()` Function

The next aggregate function is `PRODUCT()`.

### Requirement

Calculate the product of all values in the **Month Number** column.

### Formula

```DAX
Product Function =
PRODUCT('Date Table'[Month Number])
```

### Steps

1. Right-click Measures Table.
2. Select **New Measure**.
3. Name it `Product Function`.
4. Use `PRODUCT()`.
5. Select `Month Number`.
6. Press `Enter`.
7. Display it using a Card visual.

The result represents the multiplication/product of the values in the Month Number column. 

Because there are many values involved, the resulting number becomes very large.

---

# 15. `PRODUCTX()` with Filtering

Finally, the instructor demonstrates `PRODUCTX()`.

### Requirement

Calculate the product of `Month Number`, but **only for January**.

The instructor uses:

* `PRODUCTX()`
* `FILTER()`

### Formula

```DAX
Product X Function =
PRODUCTX(
    FILTER(
        'Date Table',
        'Date Table'[Month Short Name] = "Jan"
    ),
    'Date Table'[Month Number]
)
```



### Why is the result 1?

For January:

```text
Month Number = 1
```

If there are multiple January rows, the calculation is essentially:

```text
1 × 1 × 1 × 1 × ... = 1
```

Therefore, the result remains:

```text
1
```

The instructor explicitly explains this reasoning. 

---

# 16. Important Pattern: Normal Aggregate vs `X` Function

This is the **most important pattern from the lecture**.

The functions can broadly be understood as pairs:

| Normal Function | X Function   |
| --------------- | ------------ |
| `SUM()`         | `SUMX()`     |
| `MIN()`         | `MINX()`     |
| `MAX()`         | `MAXX()`     |
| `AVERAGE()`     | `AVERAGEX()` |
| `COUNT()`       | `COUNTX()`   |
| `PRODUCT()`     | `PRODUCTX()` |

The lecture repeatedly demonstrates the pattern:

```text
Normal aggregate
      ↓
Works directly on a column
```

versus:

```text
X aggregate
      ↓
Table expression / filtered table
      ↓
Expression/column to evaluate
```

For example:

```DAX
SUM('Date Table'[Length])
```

versus:

```DAX
SUMX(
    FILTER(
        'Date Table',
        'Date Table'[Month Short Name] = "Dec"
    ),
    'Date Table'[Length]
)
```

---

# 17. Role of `FILTER()`

`FILTER()` is **not itself an aggregate function**.

Its job in these examples is to create a subset of the table based on a condition.

General structure:

```DAX
FILTER(
    Table,
    Condition
)
```

Example:

```DAX
FILTER(
    'Date Table',
    'Date Table'[Month Short Name] = "Dec"
)
```

Meaning:

> Take the Date Table and retain only the rows where Month Short Name is December.

The filtered table can then be passed to an `X` function. 

---

# 18. Role of `OR()`

`OR()` is also **not an aggregate function**.

It is a logical function used when multiple conditions are possible.

General structure:

```DAX
OR(
    Condition1,
    Condition2
)
```

Example:

```DAX
OR(
    'Date Table'[Month Short Name] = "January",
    'Date Table'[Month Short Name] = "November"
)
```

This means:

> Keep the row if the month is January **OR** November.

The lecture uses this pattern with `MAXX()`, `AVERAGEX()`, and `COUNTX()`. 

---

# 19. Complete Conceptual Flow

The overall pattern taught in the lecture is:

```text
                    DAX Aggregate Functions
                            │
          ┌─────────────────┴─────────────────┐
          │                                   │
   Normal Aggregate                    X Aggregate
          │                                   │
   SUM / MIN / MAX                    SUMX / MINX / MAXX
   AVERAGE / COUNT                    AVERAGEX / COUNTX
   PRODUCT                            PRODUCTX
          │                                   │
    Direct column                  Table expression + expression
                                              │
                                      Often uses FILTER()
                                              │
                                      May use OR() conditions
```

---

# 20. Measures Created in the Lecture

The Measures Table ultimately contains measures corresponding to the aggregate functions discussed:

```text
Sum Function
Sum X Function

Min Fun
Min X Function

Max Fun
Max X Function

Average Fun
Average X Function

Count Function
Count X Function

Product Function
Product X Function
```

The instructor summarizes that these are the aggregate functions discussed, while `FILTER()` and `OR()` are supporting **non-aggregate functions**. 

---

# 21. Quick Revision Table

| Function     | What it does                        | Example from lecture               |
| ------------ | ----------------------------------- | ---------------------------------- |
| `SUM()`      | Adds values                         | Sum of `Length`                    |
| `SUMX()`     | Sum over filtered/table expression  | Sum of `Length` for December       |
| `MIN()`      | Finds minimum                       | Minimum `Length`                   |
| `MINX()`     | Minimum over filtered table         | Minimum `Length` for December      |
| `MAX()`      | Finds maximum                       | Maximum `Length`                   |
| `MAXX()`     | Maximum over filtered table         | Maximum `Month Number` for Jan/Nov |
| `AVERAGE()`  | Calculates average                  | Average `Month Number`             |
| `AVERAGEX()` | Average over filtered table         | Average Month Number for Nov/Dec   |
| `COUNT()`    | Counts column values                | Count of Month Short Name          |
| `COUNTX()`   | Counts values over table expression | Count for Jan/Dec                  |
| `PRODUCT()`  | Multiplies values                   | Product of Month Number            |
| `PRODUCTX()` | Product over filtered table         | Product for January                |
| `FILTER()`   | Filters a table based on condition  | Filter Date Table for December     |
| `OR()`       | Combines logical conditions         | January OR November                |

---

## 22. Key Takeaways for Exam/Interview

### 1. Aggregate functions

The lecture's primary focus is on:

```text
SUM
MIN
MAX
AVERAGE
COUNT
PRODUCT
```

### 2. Iterator/X versions

The corresponding iterator-style functions are:

```text
SUMX
MINX
MAXX
AVERAGEX
COUNTX
PRODUCTX
```

### 3. Filtering

The lecture repeatedly uses:

```DAX
FILTER()
```

with `X` functions to restrict the data being evaluated.

### 4. Multiple conditions

For conditions such as:

```text
January OR November
```

the lecture uses:

```DAX
OR()
```

### 5. Measures Table

Creating a dedicated **Measures Table** is demonstrated as a useful organizational practice for storing measures separately from the underlying data tables.

### 6. Card Visual

The lecture repeatedly uses a **Card visual** to display the output of each measure.

### 7. Main mental model

Remember this pattern:

```text
Column directly
    ↓
SUM / MIN / MAX / AVERAGE / COUNT / PRODUCT
```

Whereas:

```text
Filter table
    ↓
Evaluate expression
    ↓
SUMX / MINX / MAXX / AVERAGEX / COUNTX / PRODUCTX
```

This is the central idea running through essentially the entire lecture.
