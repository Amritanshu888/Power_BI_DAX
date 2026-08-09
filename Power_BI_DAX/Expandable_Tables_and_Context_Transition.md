# Expandable Tables & Default Context Behavior in Power BI

## 1. Topics Covered

This session covers two major concepts:

1. **Expandable Tables**
2. **Default Context Behavior**

   * Calculated Columns → **Row Context**
   * Measures → **Filter Context**
3. **Context Transition using `CALCULATE()`**
4. Difference between **Calculated Columns and Measures**

---

# 2. Example Data Model

The lecture uses two tables:

### Fact Sales Table

The `Fact Sales` table contains:

| Customer ID | Sales |
| ----------: | ----: |
|           1 |   100 |
|           1 |   200 |
|           1 |   150 |
|           2 |   300 |
|           2 |   250 |
|         ... |   ... |

### Meaning

* **Customer ID** → identifies the customer who purchased the product.
* **Sales** → sales value generated from the purchase.
* A customer can appear **multiple times** because the same customer can purchase multiple products or make multiple transactions.

For example:

> If Customer ID = 1 appears three times, it means that customer made three purchases/transactions.

---

# 3. Customer Dimension Table

The second table is the **Dim Customer** table.

It contains information such as:

| Customer ID | Name       | Gender | Phone |
| ----------- | ---------- | ------ | ----- |
| 1           | Customer A | Male   | XXXXX |
| 2           | Customer B | Female | XXXXX |
| 3           | Customer C | Male   | XXXXX |

### Important Point

`Customer ID` in the **Dim Customer** table acts as the **Primary Key**.

It uniquely identifies each customer.

---

# 4. Relationship Between the Tables

When we move to the **Model View**, we can see the relationship between:

* `Dim Customer`
* `Fact Sales`

The relationship is:

**One-to-Many (1:*)**

```text
Dim Customer          Fact Sales
-------------         -------------
Customer ID   1  ──── *  Customer ID
```

### Why one-to-many?

Because:

* One customer exists **once** in `Dim Customer`.
* The same customer can appear **multiple times** in `Fact Sales`.

For example:

```text
Dim Customer

Customer ID
-----------
1
2
3
```

But:

```text
Fact Sales

Customer ID
-----------
1
1
1
2
2
3
3
3
```

Therefore:

> **Dim Customer = One side**

> **Fact Sales = Many side**

---

# 5. Filter Propagation

The relationship also has a **filter direction**.

The filter propagates from:

```text
Dim Customer
     ↓
Fact Sales
```

This means if we filter a customer in the dimension table, that filter can propagate to the corresponding records in the fact table.

### Example

Suppose we filter:

```text
Customer ID = 1
```

in `Dim Customer`.

Power BI will apply that filter to `Fact Sales`.

So only the sales transactions belonging to Customer ID 1 will be considered.

---

# 6. Expandable Tables

One important concept discussed in the lecture is the idea of an **expandable table**.

An expandable table is an **invisible logical representation** of the many-side table together with columns that can be reached through relationships.

For the given model:

```text
Dim Customer (1)
       ↓
Fact Sales (Many)
```

An expandable representation exists for the **Fact Sales** table.

Conceptually, it can be thought of as containing:

### Columns from Fact Sales

* Customer ID
* Sales

### Plus columns reachable from Dim Customer

* Customer ID
* Name
* Gender
* Phone

So conceptually:

```text
Expandable Fact Sales
------------------------------------
Fact Sales columns
    +
Dim Customer columns
```

### Important

This does **not** mean that Power BI physically creates another table containing duplicated data.

It is a conceptual/logical behavior used to understand how DAX evaluates expressions and how relationships participate in calculations.

---

# 7. Why Does the Expandable Table Exist for Fact Sales?

The `Fact Sales` table is on the **many side** of the relationship.

Therefore, the expandable-table behavior applies to the fact table in this scenario.

Conceptually:

```text
Dim Customer
     1
     |
     |
     *
Fact Sales
```

The many-side table can access the related columns from the one-side dimension through the relationship.

---

# 8. Calculated Column — Default Context

Now we move to the most important part of the lecture:

> **What context does a calculated column follow by default?**

A calculated column follows **Row Context** by default.

---

# 9. Creating a Calculated Column

Let's reproduce the steps shown in the lecture.

### Step 1

Go to **Table View**.

### Step 2

Select/right-click the:

**Fact Sales** table.

### Step 3

Select:

**New Column**

### Step 4

Create the following DAX:

```DAX
Sum = SUM('Fact Sales'[Sales])
```

The exact table/column notation can vary depending on the model, but the important expression is:

```DAX
SUM(Sales)
```

---

# 10. What Happens?

After pressing **Enter**, a new calculated column called `Sum` is created.

You will notice something important:

> **The same total value appears in every row.**

For example:

| Customer ID | Sales |    Sum |
| ----------: | ----: | -----: |
|           1 |   100 | 69,905 |
|           1 |   200 | 69,905 |
|           1 |   150 | 69,905 |
|           2 |   300 | 69,905 |
|           2 |   250 | 69,905 |
|         ... |   ... | 69,905 |

The lecture's total sales value is:

**69,905**

---

# 11. Why Does the Same Value Appear Everywhere?

This happens because a **calculated column has row context by default**.

However, simply having a row context does **not** automatically cause `SUM()` to aggregate only the current row.

The expression:

```DAX
SUM('Fact Sales'[Sales])
```

evaluates the sum of the `Sales` column over the table's relevant evaluation scope.

Since there is no context transition here, the current row is not converted into an equivalent filter context for the `SUM()` aggregation.

Therefore, the overall total:

```text
69,905
```

is returned for every row.

### Key takeaway

```text
Calculated Column
       ↓
Default Row Context
       ↓
SUM() does not automatically convert
the current row into a filter
       ↓
Same overall total appears
```

---

# 12. Using CALCULATE() in a Calculated Column

Now the lecture demonstrates how to change this behavior.

The function used is:

```DAX
CALCULATE()
```

`CALCULATE()` is often described as a very powerful or "magical" DAX function because it can perform **context transition** and modify filter context.

---

# 13. Creating the Second Calculated Column

### Step 1

Right-click:

**Fact Sales**

### Step 2

Select:

**New Column**

### Step 3

Write:

```DAX
New Column =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

Then press **Enter**.

---

# 14. What Happens Now?

This time, the result is different.

The value in the new column corresponds to the **Sales value of the current row**.

For example:

| Customer ID | Sales | New Column |
| ----------: | ----: | ---------: |
|           1 |   100 |        100 |
|           1 |   200 |        200 |
|           1 |   150 |        150 |
|           2 |   300 |        300 |
|           2 |   250 |        250 |

---

# 15. Why Does CALCULATE() Change the Result?

This is because `CALCULATE()` performs **context transition**.

### Without CALCULATE()

```DAX
SUM('Fact Sales'[Sales])
```

The calculated column has a row context, but the row context isn't automatically converted into a filter context for `SUM()`.

Therefore:

```text
Total Sales = 69,905
```

is returned for every row.

---

### With CALCULATE()

```DAX
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

`CALCULATE()` converts the current **row context** into an equivalent **filter context**.

This is called:

> **Context Transition**

So if the current row has:

```text
Customer ID = 1
Sales = 100
```

the current row's context is transitioned into filter context, and the `SUM()` evaluates accordingly.

In this simple example, the resulting value is the current row's sales value.

---

# 16. Context Transition

This is one of the most important concepts from the lecture.

### Definition

**Context transition** is the process where `CALCULATE()` converts the existing **row context into filter context**.

Conceptually:

```text
Row Context
     ↓
 CALCULATE()
     ↓
Context Transition
     ↓
Filter Context
```

This is especially important when using `CALCULATE()` inside a **calculated column** or inside an iterator where row context exists.

---

# 17. Calculated Column — Important Summary

### Without CALCULATE

```DAX
Sum = SUM('Fact Sales'[Sales])
```

Result:

```text
69,905
69,905
69,905
69,905
...
```

Because there is no context transition.

### With CALCULATE

```DAX
New Column =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

Result:

```text
100
200
150
300
250
...
```

because the current row context is transitioned into filter context.

---

# 18. Now Let's Understand Measures

The lecture then moves from **calculated columns** to **measures**.

This is very important because the default behavior of a measure is different.

---

# 19. Creating a Measure

### Step 1

Go to **Report View**.

### Step 2

Expand the **Data pane**.

### Step 3

Right-click:

**Fact Sales**

### Step 4

Select:

**New Measure**

### Step 5

Create:

```DAX
Sales Measure =
SUM('Fact Sales'[Sales])
```

Then press **Enter**.

---

# 20. Default Context of a Measure

Unlike a calculated column:

> **A measure follows Filter Context.**

So:

```text
Measure
   ↓
Default Filter Context
```

A measure does not behave like a calculated column that automatically evaluates row-by-row and stores a value for every row.

Instead, a measure is evaluated **when it is used in a visual**, based on the filters and context of that visual.

---

# 21. Creating a Table Visual

The lecture demonstrates the measure using a table visual.

### Step 1

Go to **Report View**.

### Step 2

Select the **Table** visual from the Visualizations pane.

### Step 3

Drag:

```text
Customer ID
```

into the table's **Columns/Values** area.

The `Customer ID` can be taken from either the appropriate table as demonstrated, with the dimension table being the preferred modeling choice.

### Step 4

Drag:

```text
Sales Measure
```

into the visual as well.

---

# 22. Result of the Measure

Now Power BI displays the total sales for each customer.

For example:

| Customer ID | Sales Measure |
| ----------: | ------------: |
|           1 |           450 |
|           2 |           550 |
|           3 |           700 |
|         ... |           ... |
|   **Total** |    **69,905** |

The exact customer totals depend on the underlying data.

---

# 23. Why Does the Measure Give Different Values?

This happens because the measure follows **Filter Context**.

Suppose the table visual has:

```text
Customer ID = 1
```

For that particular row of the visual, Customer ID 1 creates a filter.

The measure:

```DAX
Sales Measure =
SUM('Fact Sales'[Sales])
```

is then evaluated under that filter.

Conceptually:

```text
Customer ID = 1
        ↓
Filter Context
        ↓
SUM(Sales)
        ↓
Total Sales for Customer 1
```

Then for Customer ID 2:

```text
Customer ID = 2
        ↓
Filter Context
        ↓
SUM(Sales)
        ↓
Total Sales for Customer 2
```

And so on.

---

# 24. Grand Total in a Measure

At the bottom of the table visual, Power BI also shows a **Total**.

The total is evaluated under the total-level filter context.

Therefore, the measure returns the overall sales total:

```text
69,905
```

This demonstrates an important property of measures:

> The same measure can return different results depending on the filter context in which it is evaluated.

---

# 25. Measure vs Calculated Column

This is one of the main comparisons from the lecture.

| Feature                                             | Calculated Column   | Measure                                         |
| --------------------------------------------------- | ------------------- | ----------------------------------------------- |
| Default context                                     | Row Context         | Filter Context                                  |
| Evaluation                                          | Row by row          | At query/visual evaluation time                 |
| Stored in model                                     | Yes                 | No result stored for every row                  |
| Created using                                       | New Column          | New Measure                                     |
| Automatically follows row context                   | Yes                 | No                                              |
| Automatically follows filter context                | Not by itself       | Yes                                             |
| `CALCULATE()` for context transition                | Important           | Usually not required just to respond to filters |
| Can return different values based on visual filters | Not in the same way | Yes                                             |

---

# 26. Measure with CALCULATE()

The lecture then demonstrates that `CALCULATE()` can also be used with a measure.

### Step 1

Right-click:

**Fact Sales**

### Step 2

Select:

**New Measure**

### Step 3

Create:

```DAX
Measure Using Calculate =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

Press **Enter**.

---

# 27. Compare Both Measures

Now put both measures into the same table visual:

```text
Sales Measure
```

and

```text
Measure Using Calculate
```

The results will be the same.

For example:

| Customer ID | Sales Measure | Measure Using Calculate |
| ----------: | ------------: | ----------------------: |
|           1 |           450 |                     450 |
|           2 |           550 |                     550 |
|           3 |           700 |                     700 |
|         ... |           ... |                     ... |
|       Total |        69,905 |                  69,905 |

---

# 28. Why Does CALCULATE() Not Change the Result Here?

The important point is that the measure already operates in **filter context**.

The normal measure:

```DAX
Sales Measure =
SUM('Fact Sales'[Sales])
```

already responds to the filter context created by the visual.

Adding:

```DAX
CALCULATE()
```

without adding or modifying any filters doesn't change the result in this example:

```DAX
Measure Using Calculate =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

Therefore:

```text
SUM()
```

and

```text
CALCULATE(SUM())
```

produce the same output here.

---

# 29. Why Is the Difference More Visible in a Calculated Column?

Compare the two situations.

### Calculated Column

```DAX
SUM(Sales)
```

has row context but no context transition.

Therefore, the row context doesn't automatically become a filter context for the aggregation.

Adding:

```DAX
CALCULATE(SUM(Sales))
```

causes context transition.

---

### Measure

```DAX
SUM(Sales)
```

already operates under the filter context created by the report/visual.

Therefore:

```DAX
CALCULATE(SUM(Sales))
```

doesn't produce a different result unless `CALCULATE()` is actually being used to modify the context.

---

# 30. Overall Flow of the Lecture

The entire concept can be remembered using this flow:

```text
                 DAX Calculation
                       |
          ┌────────────┴────────────┐
          ↓                         ↓
   Calculated Column             Measure
          |                         |
   Default Row Context       Default Filter Context
          |                         |
          ↓                         ↓
     SUM(Sales)                 SUM(Sales)
          |                         |
   Same overall total        Responds to filters
    in every row             from the visual
          |
          ↓
 CALCULATE(SUM(Sales))
          |
          ↓
  Context Transition
          |
          ↓
 Current row becomes
    filter context
          |
          ↓
 Current row's result
```

---

# 31. Key Concepts to Remember

### 1. Fact and Dimension Relationship

```text
Dim Customer 1 ───── * Fact Sales
```

* Dimension → One side
* Fact → Many side

---

### 2. Filter Propagation

Filter flows from:

```text
Dim Customer → Fact Sales
```

---

### 3. Expandable Table

The fact table on the many side can conceptually access:

```text
Fact Sales columns
+
Related Dim Customer columns
```

This is a **logical/invisible concept**, not a physically duplicated table.

---

### 4. Calculated Column

Default context:

> **Row Context**

Example:

```DAX
Sum =
SUM('Fact Sales'[Sales])
```

Without context transition, the aggregate can return the overall total for each row.

---

### 5. CALCULATE in Calculated Column

```DAX
New Column =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

`CALCULATE()` performs:

> **Context Transition**

```text
Row Context → Filter Context
```

---

### 6. Measure

Default context:

> **Filter Context**

Example:

```DAX
Sales Measure =
SUM('Fact Sales'[Sales])
```

It automatically responds to filters coming from the visual.

---

### 7. CALCULATE in Measure

```DAX
Measure Using Calculate =
CALCULATE(
    SUM('Fact Sales'[Sales])
)
```

In this particular example, it produces the **same result** as:

```DAX
SUM('Fact Sales'[Sales])
```

because no additional filter modification is being performed.

---

# 32. Quick Revision Table

| Concept                            | Important Point                                            |
| ---------------------------------- | ---------------------------------------------------------- |
| `Fact Sales`                       | Many side of relationship                                  |
| `Dim Customer`                     | One side of relationship                                   |
| Relationship                       | One-to-Many                                                |
| Filter direction                   | Dim Customer → Fact Sales                                  |
| Expandable table                   | Logical expansion of many-side table with related columns  |
| Calculated column                  | Default Row Context                                        |
| Measure                            | Default Filter Context                                     |
| `CALCULATE()`                      | Performs context transition and/or modifies filter context |
| `CALCULATE()` in calculated column | Converts row context → filter context                      |
| `CALCULATE()` in simple measure    | May produce same result if no filters are modified         |
| `SUM()` in calculated column       | Can return same overall total in every row                 |
| `SUM()` in measure                 | Responds to the filter context of the visual               |

---

## 33. Most Important Exam/Interview Takeaway

The core idea of this entire lecture is:

> **Calculated columns have a default row context, whereas measures are evaluated in filter context. `CALCULATE()` is important because it can perform context transition, converting row context into filter context.**

The easiest way to remember it is:

```text
Calculated Column
       ↓
 Row Context
       ↓
CALCULATE()
       ↓
Context Transition
       ↓
 Filter Context
```

Whereas:

```text
Measure
   ↓
Filter Context
   ↓
Responds to filters from
visuals, slicers, rows, columns, etc.
```

And that's the fundamental reason why the same `SUM(Sales)` expression behaves differently when used as a **calculated column** versus a **measure**.
