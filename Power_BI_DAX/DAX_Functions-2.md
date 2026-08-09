# DAX Important Functions & Context — Detailed Notes

## 1. Measures vs. Calculated Columns

Before discussing the DAX functions, the lecture highlights an important difference between **calculated columns** and **measures**.

### Calculated Columns

* Calculated columns occupy **physical space in the Power BI model**.
* Therefore, creating calculated columns increases the **model size**.
* Since the model becomes larger, report performance can also be affected.

### Measures

* Measures are **dynamic calculations**.
* They are calculated at **runtime**.
* Therefore, they occupy **negligible physical storage space** compared with calculated columns.

### Important recommendation

> If the required calculation can be achieved using a measure instead of a calculated column, prefer using a **measure**.

This is important for both **model size** and **report performance**. 

---

# 2. `SUM()` vs `SUMX()`

This is presented as an important **Power BI/DAX interview question**.

The key idea is that these two functions may produce the same result in some situations, but they work differently.

---

## 2.1 `SUM()`

### Syntax

```DAX
SUM(<column>)
```

`SUM()` accepts **one argument**, which must be a **column reference**.

### Example

Suppose we want the sum of `Month Number` from the Date table:

```DAX
SUM Function =
SUM('Date Table'[Month Number])
```

The function simply calculates the sum of the values contained in that column. 

---

## 2.2 Creating the `SUM()` measure in Power BI

The lecture demonstrates the following process:

1. Right-click the **Date Table**.
2. Select **New Measure**.
3. Name it something like:
   `SUM Function`
4. Enter:

```DAX
SUM Function =
SUM('Date Table'[Month Number])
```

5. Press **Enter**.
6. To display it:

   * Click a blank area on the report canvas.
   * Select the **Card visual**.
   * Add the `SUM Function` measure to the card.

The lecture also demonstrates formatting the card by going to:

**Format visual → General → Effects → Border → On**. 

---

# 3. `SUMX()`

`SUMX()` is an **iterator function**.

### Syntax

```DAX
SUMX(<table>, <expression>)
```

It requires **two arguments**:

1. **Table**
2. **Expression**

Example:

```DAX
SUMX(
    'Date Table',
    'Date Table'[Month Number]
)
```

The first argument tells DAX **which table to iterate over**.

The second argument tells DAX **what expression should be evaluated for every row**. 

---

## 3.1 Creating `SUMX()` in Power BI

Steps shown in the lecture:

1. Right-click `Measures Table 1`.
2. Select **New Measure**.
3. Name it:
   `SUMX Function`
4. Write:

```DAX
SUMX Function =
SUMX(
    'Date Table',
    'Date Table'[Month Number]
)
```

5. Press **Enter**.
6. Create/copy another Card visual.
7. Remove the existing `SUM Function`.
8. Add `SUMX Function`.

Initially, both measures return the **same value**. 

---

# 4. Main Difference Between `SUM()` and `SUMX()`

This is one of the most important concepts from the lecture.

### `SUM()`

`SUM()` simply calculates the sum of values in a **column**.

```DAX
SUM('Date Table'[Month Number])
```

It does **not** allow you to provide an arbitrary expression as its argument.

### `SUMX()`

`SUMX()`:

1. Iterates through the specified table.
2. Evaluates the expression **for every row**.
3. Adds all the evaluated results.
4. Returns the final sum.

This is why `SUMX()` is called an **iterative function**. 

---

# 5. Why `SUMX()` Is More Flexible

Suppose the requirement is:

> Calculate **twice** the sum of `Month Number`.

With `SUMX()`:

```DAX
SUMX Function =
SUMX(
    'Date Table',
    'Date Table'[Month Number] * 2
)
```

### What happens?

For every row:

```text
Month Number × 2
```

is calculated.

Then all those results are summed.

Conceptually:

```text
Row 1 → Month Number × 2
Row 2 → Month Number × 2
Row 3 → Month Number × 2
...
↓
SUM of all results
```

The lecture demonstrates that this produces a different result (4766 in the demonstrated dataset). 

---

## 5.1 Why can't we do this with `SUM()`?

Trying:

```DAX
SUM(
    'Date Table'[Month Number] * 2
)
```

produces an error.

The reason is:

> `SUM()` only accepts a **column reference** as its argument.

It cannot directly accept an expression such as:

```DAX
Column * 2
```

`SUMX()` can accept an expression. 

---

# 6. Row Context

The concept introduced through `SUMX()` is **Row Context**.

Since `SUMX()` evaluates its expression:

```text
row by row
```

it operates in **row context**.

For example:

```DAX
SUMX(
    'Date Table',
    'Date Table'[Month Number] * 2
)
```

The expression:

```DAX
'Date Table'[Month Number] * 2
```

is evaluated separately for each row.

Then the results are aggregated.

### Important point

This concept isn't limited to `SUMX()`.

Other iterator functions also work similarly, such as:

* `PRODUCTX()`
* `AVERAGEX()`
* `COUNTX()`
* other `X` iterator functions.

### Remember

> **X functions generally indicate iteration over rows.**

The lecture specifically describes these functions as **iterative functions working in row context**. 

---

# 7. Two Main DAX Contexts

The lecture introduces two major contexts in DAX:

### 1. Row Context

Calculation/expression is evaluated **row by row**.

Example:

```DAX
SUMX(
    'Date Table',
    'Date Table'[Month Number] * 2
)
```

### 2. Filter Context

Calculations are performed after the applicable **filters** have been applied.

These two contexts are extremely important in DAX and are common interview topics. 

---

# 8. Filter Context

The lecture demonstrates filter context using a **Matrix visual**.

## Creating the Matrix

Steps:

1. Click the **+** icon to create a new report page.
2. Click a blank area on the canvas.
3. Select **Matrix** visual.
4. Resize the matrix.

The lecture notes that a **Matrix visual is also referred to as a pivot table**. 

---

## Adding fields

### Rows

Add:

```text
Month Short Name
```

to the **Rows** bucket.

### Columns

Add:

```text
Weekday Short Name
```

to the **Columns** bucket.

### Values

Add:

```text
Month Number
```

to the **Values** bucket.

Power BI then shows the sum of Month Number for different combinations of:

```text
Month × Weekday
```



---

# 9. Understanding Filter Context with an Example

Suppose one cell shows:

```text
20
```

Before calculating this value, Power BI applies the filters corresponding to that cell.

For example:

```text
Month = April
Weekday = Friday
```

Then:

```text
SUM(Month Number)
```

is calculated only over the rows satisfying those filters.

Similarly, if another cell corresponds to:

```text
Month = December
Weekday = Saturday
```

Power BI first applies these filters and then calculates the sum. 

### Definition

> **Filter Context** is the set of filters that are applied to the data before a calculation takes place.

This is why matrix/table visuals naturally create different filter contexts for different cells. 

---

# 10. `CALCULATETABLE()`

The next concept is creating a **new calculated table from an existing table using filters**.

### Requirement

The Date table contains data for:

```text
January
February
March
...
December
```

Suppose we want a new table containing data for **only December**.

This can be achieved using:

```DAX
CALCULATETABLE()
```



---

## 10.1 Creating a calculated table

Steps:

1. Go to **Report View**.
2. Go to the **Modeling** tab.
3. Select **New Table**.
4. Name the table, for example:

```text
Calculated Table
```

5. Enter:

```DAX
Calculated Table =
CALCULATETABLE(
    'Date Table',
    'Date Table'[Month Short Name] = "December"
)
```

6. Press **Enter**.

A new calculated table is created. 

---

## 10.2 Verify the calculated table

Go to:

**Table View → Calculated Table**

The table should now contain records only for:

```text
December
```

rather than all 12 months. 

---

## 10.3 Applying multiple filters

You can provide multiple filters to `CALCULATETABLE()`.

For example:

```DAX
Calculated Table =
CALCULATETABLE(
    'Date Table',
    'Date Table'[Month Short Name] = "December",
    'Date Table'[Weekday Short Name] = "Friday"
)
```

Now the resulting table contains data only where:

```text
Month = December
AND
Weekday = Friday
```

The lecture explicitly demonstrates adding the second filter after a comma. 

---

# 11. `CALCULATE()`

`CALCULATE()` is one of the **most important DAX functions** discussed in the lecture.

It is described as a very powerful function because it can **modify the context in which a calculation is performed**.

It is also specifically highlighted as an important **Power BI interview question**. 

---

## 11.1 Basic syntax

```DAX
CALCULATE(
    <expression>,
    <filter1>,
    <filter2>,
    ...
)
```

The first argument is the **expression**.

Additional arguments are filters/modifiers.

---

## 11.2 Example

Requirement:

> Calculate the sum of Month Number only for December.

Measure:

```DAX
Calculate Function =
CALCULATE(
    SUM('Date Table'[Month Number]),
    'Date Table'[Month Short Name] = "December"
)
```

### Steps demonstrated

1. Right-click `Measures Table 1`.
2. Select **New Measure**.
3. Name it `Calculate Function`.
4. Type `CALCULATE`.
5. Provide the expression:

```DAX
SUM('Date Table'[Month Number])
```

6. Add a filter:

```DAX
'Date Table'[Month Short Name] = "December"
```

7. Press **Enter**.
8. Add the measure to the table visual. 

---

# 12. `CALCULATE()` Modifies Filter Context

This is a **very important concept**.

Suppose the table visual already has:

```text
Month = January
Month = February
Month = March
...
```

But the measure contains:

```DAX
'Date Table'[Month Short Name] = "December"
```

Then the `CALCULATE()` filter causes the measure to calculate specifically for **December**.

Therefore, the measure can show the December value even when the surrounding visual has other month values.

The lecture explains this as:

> `CALCULATE()` modifies the context in which filters work or calculations are evaluated. 

---

# 13. `ALL()`

`ALL()` is another important DAX function.

It is commonly used with `CALCULATE()` to **remove filters**.

### Syntax

```DAX
ALL(<table>)
```

or

```DAX
ALL(<column>)
```

The lecture specifically points out that `ALL()` can accept either a **table name or column name**. 

---

## 13.1 Example

Requirement:

> Calculate the total Month Number while ignoring filters applied to the Date table.

Measure:

```DAX
ALL Function =
CALCULATE(
    SUM('Date Table'[Month Number]),
    ALL('Date Table')
)
```

### What happens?

`ALL('Date Table')` removes filters from the Date table.

Therefore, when this measure is placed against different months, it shows the **same grand total** for every month.

In the lecture's dataset, that total is:

```text
2383
```



---

# 14. Why `ALL()` Returns the Same Value

Suppose the visual contains:

| Month     |      Normal Sum | ALL Function |
| --------- | --------------: | -----------: |
| April     |     April value |         2383 |
| August    |    August value |         2383 |
| September | September value |         2383 |
| ...       |             ... |         2383 |

The normal sum respects the month filter.

But:

```DAX
ALL('Date Table')
```

removes those Date Table filters.

Therefore:

```text
Every row → calculate total over entire Date Table
```

Hence every row gets the same grand total.

---

# 15. `DIVIDE()`

The next function is `DIVIDE()`.

It is used to perform division safely and is particularly useful for calculating **percentages/contributions**.

### Syntax

```DAX
DIVIDE(
    <numerator>,
    <denominator>,
    <alternative_result>
)
```

The third argument is optional.

The lecture notes that it can be used when the denominator is zero or another division situation needs an alternative result. 

---

# 16. Calculating Percentage Contribution

Requirement:

> Find the percentage contribution of each month to the overall total.

The basic mathematical idea is:

```text
Month Value
────────────── × 100
Total Value
```

In the example:

```text
Total = 2383
```

---

## 16.1 Numerator

The numerator is:

```DAX
SUM('Date Table'[Month Number])
```

---

## 16.2 Denominator

The denominator needs to be the **overall total**, regardless of the current month filter.

Therefore:

```DAX
CALCULATE(
    SUM('Date Table'[Month Number]),
    ALL('Date Table')
)
```

is used.

---

## 16.3 Complete measure

Conceptually:

```DAX
Divide Function =
DIVIDE(
    SUM('Date Table'[Month Number]),
    CALCULATE(
        SUM('Date Table'[Month Number]),
        ALL('Date Table')
    )
) * 100
```

This gives the percentage contribution of each month. 

---

## 16.4 Example from the lecture

For April:

```text
April contribution = 120
Total = 2383
```

Therefore:

```text
120 / 2383 × 100
≈ 5.04%
```

For September:

```text
September contribution = 270
```

Therefore:

```text
270 / 2383 × 100
≈ 11.33%
```

So the measure tells us what percentage of the total is contributed by each month. 

---

# 17. `ALL()` vs `ALLSELECTED()`

This is another **important interview question**.

The lecture demonstrates the difference by introducing a **slicer**.

---

# 18. `ALLSELECTED()`

Example measure:

```DAX
ALL Selected Function =
CALCULATE(
    SUM('Date Table'[Month Number]),
    ALLSELECTED('Date Table')
)
```

Like `ALL()`, `ALLSELECTED()` can work with a table or column.

Initially, if there are no explicit external filters, the values may appear similar to `ALL()`. 

---

# 19. Understanding `ALLSELECTED()` with a Slicer

A **slicer** is a visual filter.

### Steps demonstrated

1. Click a blank area on the canvas.
2. Select **Slicer**.
3. Add:

```text
Weekday Short Name
```

to the slicer.
4. Select a value such as:

```text
Friday
```

Now an explicit filter is applied.

### Result

For `ALL()`:

```text
2383
```

still remains unchanged.

For `ALLSELECTED()`:

```text
344
```

appears because the slicer's explicit filter is respected. 

---

## 19.1 Example with Monday

If the slicer is changed to:

```text
Monday
```

the `ALLSELECTED()` result changes to:

```text
336
```

while `ALL()` continues to show:

```text
2383
```

This demonstrates the important distinction.

---

# 20. `ALL()` vs `ALLSELECTED()` — Core Difference

| Function        | Behavior                                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------ |
| `ALL()`         | Ignores filters                                                                                              |
| `ALLSELECTED()` | Respects the explicit/external filters selected by the user while removing the relevant visual-level context |

### Easy way to remember

**ALL**

> "Ignore filters."

**ALLSELECTED**

> "Ignore the current visual context, but respect what the user explicitly selected."

The slicer example is the key demonstration from the lecture. 

---

# 21. `ALLEXCEPT()`

The next function is:

```DAX
ALLEXCEPT()
```

It is also used with `CALCULATE()` to modify filter context.

### Syntax

```DAX
ALLEXCEPT(
    <table>,
    <column1>,
    <column2>,
    ...
)
```

It requires:

1. A table.
2. One or more columns whose filters should be retained.

All other filters from that table are removed. 

---

# 22. Example of `ALLEXCEPT()`

The lecture creates:

```DAX
ALLEXCEPT Function =
CALCULATE(
    SUM('Date Table'[Month Number]),
    ALLEXCEPT(
        'Date Table',
        'Date Table'[Weekday Short Name]
    )
)
```

The important part is:

```DAX
ALLEXCEPT(
    'Date Table',
    'Date Table'[Weekday Short Name]
)
```

This means:

> Remove filters from the Date table **except** the filter on `Weekday Short Name`.

---

# 23. Understanding `ALLEXCEPT()` Through the Matrix

The lecture adds both:

```text
Month Short Name
Weekday Short Name
```

to the visual.

The result is:

### Month filter

❌ Ignored.

### Weekday filter

✅ Retained.

Therefore, the value changes according to the weekday but does not change according to the month.

For example:

```text
Friday → 344
Monday → 336
```

regardless of which month is being displayed. 

---

# 24. Changing `ALLEXCEPT()` to Month

The lecture then changes:

```DAX
'Date Table'[Weekday Short Name]
```

to:

```DAX
'Date Table'[Month Short Name]
```

So conceptually:

```DAX
ALLEXCEPT(
    'Date Table',
    'Date Table'[Month Short Name]
)
```

Now the behavior reverses.

### Month filter

✅ Retained.

### Weekday filter

❌ Ignored.

Therefore:

* Values vary by month.
* Values don't respond to the weekday filter.

This demonstrates exactly what `ALLEXCEPT()` means: **remove every filter except the specified one(s)**. 

---

# 25. Final Comparison: `ALL`, `ALLSELECTED`, `ALLEXCEPT`

This is the most important comparison from the lecture.

| Function        | What it does                                                             | Example behavior                                             |
| --------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------ |
| `ALL()`         | Removes filters                                                          | Returns overall total regardless of visual filters           |
| `ALLSELECTED()` | Respects explicit/user selections while modifying current visual context | Responds to slicer selection                                 |
| `ALLEXCEPT()`   | Removes all filters except specified column(s)                           | Keeps Month filter but removes Weekday filter, or vice versa |

### Memory trick

```text
ALL
↓
Remove ALL filters

ALLSELECTED
↓
Respect what the user SELECTED

ALLEXCEPT
↓
Remove ALL filters EXCEPT specified ones
```

The lecture emphasizes that these functions are generally used with `CALCULATE()` to **modify the context in which an expression is evaluated**. 

---

# 26. Complete DAX Concepts Covered

The lecture essentially builds the following chain:

```text
DAX Functions
     │
     ├── SUM()
     │
     ├── SUMX()
     │      └── Iteration
     │           └── Row Context
     │
     ├── Filter Context
     │
     ├── CALCULATETABLE()
     │      └── Create filtered calculated tables
     │
     ├── CALCULATE()
     │      └── Modify filter context
     │
     ├── ALL()
     │      └── Remove filters
     │
     ├── DIVIDE()
     │      └── Percentage calculations
     │
     ├── ALLSELECTED()
     │      └── Respect explicit selections
     │
     └── ALLEXCEPT()
            └── Remove all filters except specified ones
```

---

# 27. Important Interview Questions

Based on the lecture, you should be prepared for these:

### Q1. What is the difference between `SUM()` and `SUMX()`?

**Answer:**

`SUM()` accepts a column and directly calculates its sum, whereas `SUMX()` is an iterator that evaluates an expression row by row and then sums the results.

---

### Q2. Why can't we use an expression inside `SUM()`?

Because `SUM()` accepts a **column reference**, not an arbitrary expression.

For example:

```DAX
SUM('Date Table'[Month Number])
```

is valid, but:

```DAX
SUM('Date Table'[Month Number] * 2)
```

is not.

Use:

```DAX
SUMX(
    'Date Table',
    'Date Table'[Month Number] * 2
)
```

instead.

---

### Q3. What is Row Context?

Row context means that a DAX expression is evaluated **for each individual row**.

Iterator functions such as `SUMX()` operate in row context.

---

### Q4. What is Filter Context?

Filter context is the collection of filters applied to the data **before a calculation is evaluated**.

For example, a Matrix cell can have:

```text
Month = April
Weekday = Friday
```

and the calculation occurs within that filtered context.

---

### Q5. What does `CALCULATE()` do?

`CALCULATE()` modifies the context in which an expression is evaluated by applying or modifying filters.

---

### Q6. Why is `CALCULATE()` called a powerful/magical DAX function?

Because it allows you to **modify filter context**, making it possible to perform calculations under a different filtering context.

---

### Q7. What does `ALL()` do?

It removes filters from the specified table or column.

Example:

```DAX
CALCULATE(
    SUM('Date Table'[Month Number]),
    ALL('Date Table')
)
```

returns the overall total regardless of the current Date Table filters.

---

### Q8. What does `DIVIDE()` do?

It divides a numerator by a denominator and optionally allows an alternative result for situations such as division by zero.

---

### Q9. Difference between `ALL()` and `ALLSELECTED()`?

`ALL()` removes filters, while `ALLSELECTED()` retains explicit selections such as slicer selections while modifying the current visual context.

---

### Q10. What does `ALLEXCEPT()` do?

It removes filters from a table **except** for the columns explicitly specified.

---

# 28. One-Page Revision Sheet

```text
SUM()
→ Takes a column
→ Directly calculates sum
→ Cannot directly accept an expression

SUMX()
→ Takes table + expression
→ Iterates row by row
→ Evaluates expression for every row
→ Then aggregates results
→ Works with Row Context

ROW CONTEXT
→ Calculation happens row by row
→ Common with X/iterator functions

FILTER CONTEXT
→ Filters are applied before calculation
→ Matrix cells naturally create different filter contexts

CALCULATETABLE()
→ Creates a calculated table
→ Based on an existing table + filters

CALCULATE()
→ Modifies filter context
→ Very important DAX function
→ Can apply/modify filters

ALL()
→ Removes filters
→ Often used to calculate grand totals

DIVIDE()
→ Numerator / Denominator
→ Optional alternative result
→ Useful for percentage calculations

ALLSELECTED()
→ Respects explicit/user selections
→ Example: slicer selections
→ Different from ALL()

ALLEXCEPT()
→ Removes all filters
→ Except specified column(s)
```

### Most important conceptual relationship

```text
SUMX()
   ↓
Iteration
   ↓
Row Context

CALCULATE()
   ↓
Modify Filter Context
   ↓
ALL / ALLSELECTED / ALLEXCEPT
```

The lecture closes by emphasizing that these functions—particularly **`CALCULATE()`, `ALL()`, `ALLSELECTED()`, and `ALLEXCEPT()`**—are important both for practical DAX calculations and for **Power BI interviews**. 
