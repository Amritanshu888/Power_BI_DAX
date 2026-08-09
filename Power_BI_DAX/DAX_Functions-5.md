# DAX — SAMEPERIODLASTYEAR Function

## 1. Introduction

In this lecture, we learn about the **`SAMEPERIODLASTYEAR()`** DAX function.

The function is used in **time-intelligence calculations** when we want to retrieve a value for the **same date/period in the previous year**.

The lecture builds on the previously discussed measures:

* Total QTD
* Total MTD
* Total YTD

The same `SAMEPERIODLASTYEAR()` technique can be combined with all of these measures.

---

# 2. Example Setup

The lecture uses the **same Calendar table** from the previous lecture.

The objective is to create a table visual that allows us to compare:

> Current period value vs. the value for the same period in the previous year.

For example:

| Date        | Total Profit | Same Period Last Year Profit |
| ----------- | -----------: | ---------------------------: |
| 01-Jan-2022 |           $6 |                        Blank |
| 01-Jan-2023 |           $2 |                           $6 |
| 01-Jan-2024 |           $9 |                           $2 |
| 01-Jan-2025 |            — |                           $9 |

The important idea is:

**The value in the "Same Period Last Year" column comes from exactly the same date one year earlier.**

---

# 3. Creating the Initial Table Visual

First, create a table visual to display dates and profit.

### Steps

1. Open the Power BI report.
2. Expand the visualization pane.
3. Select the **Table** visual.
4. A blank table visual is added to the canvas.
5. Select the table visual.
6. From the Data pane, select the **Date** column from the Calendar table.
7. The available dates are now displayed in the table.
8. Select the **Profit** column.
9. Power BI automatically aggregates it as **Sum of Profit**.
10. Rename `Sum of Profit` to **Total Profit**.

### Renaming the column

1. Double-click `Sum of Profit`.
2. Press `Ctrl + A`.
3. Enter:

```text
Total Profit
```

Now the table contains the total profit for each date.

---

# 4. Requirement: Profit for the Same Period Last Year

Suppose:

* Profit on **01-Jan-2022 = $6**
* Profit on **01-Jan-2023 = $2**

We want the 2023 row to show:

| Date        | Total Profit | SPLY Total Profit |
| ----------- | -----------: | ----------------: |
| 01-Jan-2023 |           $2 |                $6 |

Here, `$6` is the profit from **01-Jan-2022**, which is the same date in the previous year.

This is where `SAMEPERIODLASTYEAR()` is useful.

---

# 5. SAMEPERIODLASTYEAR() Function

### Syntax

```DAX
SAMEPERIODLASTYEAR(<dates>)
```

It returns a table containing dates shifted **one year backward** from the dates in the current filter context.

For example:

```text
01-Jan-2025 → 01-Jan-2024
01-Jan-2024 → 01-Jan-2023
01-Jan-2023 → 01-Jan-2022
```

If the previous year's date does not exist in the available data, the result will be blank.

---

# 6. Creating SPLY Total Profit

Create a new measure.

### Steps

1. Right-click the **Calendar table**.
2. Select **New Measure**.
3. Name the measure:

```DAX
SPLY Total Profit
```

4. Use `CALCULATE()` to modify the date context.

### Measure

```DAX
SPLY Total Profit =
CALCULATE(
    SUM('Calendar'[Profit]),
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

> The exact table/column names should match your model.

---

# 7. Understanding the Measure

The measure has two important parts.

### Part 1 — Calculate Profit

```DAX
SUM('Calendar'[Profit])
```

This calculates the total profit.

### Part 2 — Shift the Date Context

```DAX
SAMEPERIODLASTYEAR('Calendar'[Date])
```

This moves the current date context one year backward.

### Combined

```DAX
CALCULATE(
    SUM('Calendar'[Profit]),
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

`CALCULATE()` evaluates total profit after changing the date filter to the corresponding period from the previous year.

---

# 8. Adding SPLY Total Profit to the Table

After creating the measure:

1. Click the dropdown/expand area as needed.
2. Click the blank area of the canvas.
3. Select the table visual.
4. From the Data pane, check:

```text
SPLY Total Profit
```

Now the table contains both:

* Total Profit
* SPLY Total Profit

---

# 9. Why Are Some SPLY Values Blank?

Suppose the first year available in the dataset is **2022**.

For:

```text
01-Jan-2022
```

`SAMEPERIODLASTYEAR()` looks for:

```text
01-Jan-2021
```

But 2021 data doesn't exist.

Therefore:

```text
SPLY Total Profit = Blank
```

### Example

| Date        | Total Profit | SPLY Total Profit |
| ----------- | -----------: | ----------------: |
| 01-Jan-2022 |           $6 |             Blank |
| 02-Jan-2022 |          $10 |             Blank |

The blanks are expected because there is no corresponding previous-year data.

---

# 10. Using a Date Hierarchy and Slicer

The lecture then demonstrates the comparison across multiple years.

The objective is to compare **01 January** across:

* 2022
* 2023
* 2024
* 2025

### Steps

1. Click a blank area of the canvas.
2. Add a **Slicer** visual.
3. Select the slicer.
4. Add the **Date** column from the Calendar table.
5. Open the dropdown associated with the Date field.
6. Change it from the normal Date field to **Date Hierarchy**.
7. Expand the hierarchy through:

   * Year
   * Quarter
   * Month
   * Day

Then select:

```text
2022 → Q1 → January → 1
2023 → Q1 → January → 1
2024 → Q1 → January → 1
2025 → Q1 → January → 1
```

Use **Ctrl + click** to select multiple dates/years where necessary.

---

# 11. Comparing the Same Date Across Years

After selecting 01 January for all four years, the table demonstrates the behavior of `SAMEPERIODLASTYEAR()`.

### 01-Jan-2022

Current-year profit:

```text
$6
```

Previous year's corresponding date:

```text
01-Jan-2021
```

No data exists.

Therefore:

```text
SPLY Total Profit = Blank
```

---

### 01-Jan-2023

Current-year profit:

```text
$2
```

Previous year's corresponding date:

```text
01-Jan-2022
```

Profit was:

```text
$6
```

Therefore:

```text
SPLY Total Profit = $6
```

---

### 01-Jan-2024

Current-year profit:

```text
$9
```

Previous year's corresponding date:

```text
01-Jan-2023
```

Profit was:

```text
$2
```

Therefore:

```text
SPLY Total Profit = $2
```

---

### 01-Jan-2025

The same logic applies.

The SPLY value is the profit on:

```text
01-Jan-2024
```

So the table effectively allows year-over-year comparison.

---

# 12. Key Concept — How SAMEPERIODLASTYEAR Works

Think of the function as:

```text
Current Date
      ↓
Move exactly 1 year backward
      ↓
Retrieve the value for that date/period
```

For example:

```text
2025 → 2024
2024 → 2023
2023 → 2022
2022 → 2021
```

If the previous year's data is unavailable:

```text
Result = BLANK
```

---

# 13. SAMEPERIODLASTYEAR with Total MTD

The lecture next demonstrates that `SAMEPERIODLASTYEAR()` doesn't have to be used only with simple aggregations.

It can also be combined with existing time-intelligence measures such as **Total MTD**.

The requirement is now:

> Find the Total MTD value for the same period last year.

---

# 14. Creating SPLY Total MTD

First, the existing measures are removed from the table visual.

Create a new measure:

```text
SPLY Total MTD
```

The measure uses the previously created `Total MTD` measure.

### DAX

```DAX
SPLY Total MTD =
CALCULATE(
    [Total MTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

The important difference here is that we are **not calculating SUM(Profit) directly**.

Instead, we reuse the existing:

```DAX
[Total MTD]
```

measure.

---

# 15. Understanding SPLY Total MTD

The structure is:

```DAX
CALCULATE(
    [Total MTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

### Step 1

`[Total MTD]` calculates the month-to-date value.

### Step 2

`SAMEPERIODLASTYEAR()` shifts the date context one year backward.

### Step 3

`CALCULATE()` evaluates `[Total MTD]` under that previous-year date context.

Therefore, the result is:

> **Total MTD for the same period in the previous year.**

---

# 16. Adding the MTD Measures to the Table

After creating the measure:

1. Select the table visual.
2. Add:

```text
Total MTD
```

3. Add:

```text
SPLY Total MTD
```

Now the table can compare current MTD and previous-year MTD values.

---

# 17. Example — SPLY Total MTD

For **01-Jan-2022**:

* Total MTD = `$8`
* Same period last year = 01-Jan-2021
* No 2021 data exists

Therefore:

```text
SPLY Total MTD = Blank
```

For **01-Jan-2023**:

* Current Total MTD = `$8`
* Previous year's Total MTD = `$8`

Therefore:

```text
SPLY Total MTD = $8
```

For **01-Jan-2024**:

* Current Total MTD = `$1`
* Previous year's Total MTD = `$8`

Therefore:

```text
SPLY Total MTD = $8
```

---

# 18. SAMEPERIODLASTYEAR with Total QTD

The same technique can be applied to **Total QTD**.

Remove the MTD measures from the table and create a new measure.

### Measure

```DAX
SPLY Total QTD =
CALCULATE(
    [Total QTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

Again, the exact name of the existing QTD measure should match your model.

---

# 19. Understanding SPLY Total QTD

The calculation works as follows:

```text
Current Date
      ↓
SAMEPERIODLASTYEAR()
      ↓
Corresponding date one year earlier
      ↓
Calculate [Total QTD]
```

So the measure returns:

> **Quarter-to-date value for the corresponding period in the previous year.**

---

# 20. Example — SPLY Total QTD

For **01-Jan-2022**:

```text
Total QTD = $1
```

The previous year's corresponding date is 01-Jan-2021, for which there is no data.

Therefore:

```text
SPLY Total QTD = Blank
```

For **01-Jan-2023**:

```text
Total QTD = $3
SPLY Total QTD = $1
```

Because the QTD value on 01-Jan-2022 was `$1`.

For **01-Jan-2024**:

```text
Total QTD = $4
SPLY Total QTD = $3
```

Because the QTD value on 01-Jan-2023 was `$3`.

The same logic applies to 2025.

---

# 21. SAMEPERIODLASTYEAR with Total YTD

The final demonstration is with **Total YTD**.

Remove:

* Total QTD
* SPLY Total QTD

from the table.

Then add:

* Total YTD
* SPLY Total YTD

---

# 22. Creating SPLY Total YTD

Create another measure.

### DAX

```DAX
SPLY Total YTD =
CALCULATE(
    [Total YTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

Here:

```DAX
[Total YTD]
```

is the previously created YTD measure.

---

# 23. Understanding SPLY Total YTD

The calculation is:

```text
Current date
     ↓
SAMEPERIODLASTYEAR()
     ↓
Same date one year earlier
     ↓
Calculate Total YTD
```

Therefore, it returns:

> **Year-to-date value for the same period in the previous year.**

---

# 24. Example — SPLY Total YTD

For **01-Jan-2022**:

```text
Total YTD = $1
```

Since 01-Jan-2021 doesn't exist in the dataset:

```text
SPLY Total YTD = Blank
```

For **01-Jan-2023**:

```text
Total YTD = $3
SPLY Total YTD = $1
```

Because the YTD value on 01-Jan-2022 was `$1`.

For **01-Jan-2024**:

```text
Total YTD = $4
SPLY Total YTD = $3
```

Because the YTD value on 01-Jan-2023 was `$3`.

The same comparison can be performed for 2025.

---

# 25. Complete Measures Covered

The lecture demonstrates the following pattern.

### SPLY Total Profit

```DAX
SPLY Total Profit =
CALCULATE(
    SUM('Calendar'[Profit]),
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

### SPLY Total MTD

```DAX
SPLY Total MTD =
CALCULATE(
    [Total MTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

### SPLY Total QTD

```DAX
SPLY Total QTD =
CALCULATE(
    [Total QTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

### SPLY Total YTD

```DAX
SPLY Total YTD =
CALCULATE(
    [Total YTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

---

# 26. General Pattern to Remember

The most important pattern from this lecture is:

```DAX
SPLY Measure =
CALCULATE(
    [Existing Measure],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

For example:

```DAX
SPLY Sales =
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

```DAX
SPLY MTD =
CALCULATE(
    [Total MTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

```DAX
SPLY QTD =
CALCULATE(
    [Total QTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

```DAX
SPLY YTD =
CALCULATE(
    [Total YTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

---

# 27. Important Observations

### 1. `SAMEPERIODLASTYEAR()` shifts the date context

It moves the current date/period back by **one year**.

### 2. It is normally used with `CALCULATE()`

The common structure is:

```DAX
CALCULATE(
    <measure>,
    SAMEPERIODLASTYEAR(<date column>)
)
```

### 3. It can work with existing measures

You don't have to rewrite the entire calculation.

For example:

```DAX
CALCULATE(
    [Total YTD],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

### 4. It works with different types of calculations

It can be combined with:

* Total Profit
* Total Sales
* Total MTD
* Total QTD
* Total YTD
* Other numerical measures

### 5. Missing previous-year data produces blanks

If the corresponding date from the previous year isn't available in the dataset, the SPLY result will be blank.

---

# 28. Business Use Cases

`SAMEPERIODLASTYEAR()` is particularly useful for **year-over-year (YoY) analysis**.

For example:

### Sales comparison

```text
Sales this year vs Sales last year
```

### Profit comparison

```text
Profit this year vs Profit last year
```

### MTD comparison

```text
MTD this year vs MTD same period last year
```

### QTD comparison

```text
QTD this year vs QTD same period last year
```

### YTD comparison

```text
YTD this year vs YTD same period last year
```

This allows businesses to understand whether performance has improved or declined compared with the previous year.

---

# 29. Final Concept Summary

The entire lecture can be summarized as:

```text
SAMEPERIODLASTYEAR()
          ↓
Moves current date context
one year backward
          ↓
CALCULATE()
          ↓
Recalculates the required measure
under that previous-year context
```

For example:

```text
01-Jan-2025
     ↓
SAMEPERIODLASTYEAR()
     ↓
01-Jan-2024
     ↓
Calculate Profit / MTD / QTD / YTD
```

So the core formula to remember is:

```DAX
CALCULATE(
    [Measure],
    SAMEPERIODLASTYEAR('Calendar'[Date])
)
```

**Key takeaway:** `SAMEPERIODLASTYEAR()` is a DAX time-intelligence function used to retrieve the value corresponding to the **same period in the previous year**, and it can be combined with existing measures such as **Profit, MTD, QTD, and YTD**.
