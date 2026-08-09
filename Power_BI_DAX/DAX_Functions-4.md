# DAX Time Intelligence Functions — Detailed Notes

This lecture covers **three important DAX Time Intelligence functions** used in Power BI:

1. `TOTALMTD()` — Month-to-Date
2. `TOTALQTD()` — Quarter-to-Date
3. `TOTALYTD()` — Year-to-Date

The lecture demonstrates these functions using a manually created **Calendar Table** and a randomly generated **Profit** column.

---

# 1. Creating the Calendar Table

Since there is no dataset available initially, the first step is to create a calendar table.

### Steps

1. Open **Power BI Desktop**.
2. Select **Blank Report**.
3. Go to the **Modeling** tab.
4. Click **New Table**.
5. Create the calendar table using the `CALENDAR()` function.

### DAX

```DAX
Calendar Table =
CALENDAR(
    DATE(2022, 1, 1),
    DATE(2025, 12, 31)
)
```

### What this does

The `CALENDAR()` function creates a table containing a continuous sequence of dates between the specified start and end dates.

Here:

* Start Date = **1 January 2022**
* End Date = **31 December 2025**

Therefore, the table contains dates covering the entire period:

**2022 → 2023 → 2024 → 2025**

The resulting table initially contains one column:

| Date        |
| ----------- |
| 01-Jan-2022 |
| 02-Jan-2022 |
| 03-Jan-2022 |
| ...         |
| 31-Dec-2025 |

---

# 2. Creating the Profit Column

The lecture then creates a calculated column containing random profit values.

### Steps

1. Right-click **Calendar Table**.
2. Select **New Column**.
3. Name the column `Profit`.
4. Use the `RANDBETWEEN()` function.

### DAX

```DAX
Profit = RANDBETWEEN(1, 10)
```

This generates a random integer between:

* Minimum = `1`
* Maximum = `10`

So each date gets a randomly generated profit value between **1 and 10**.

For example:

| Date        | Profit |
| ----------- | -----: |
| 01-Jan-2022 |      6 |
| 02-Jan-2022 |      7 |
| 03-Jan-2022 |      4 |
| 04-Jan-2022 |      8 |
| ...         |    ... |

> **Important:** `RANDBETWEEN()` generates random values, so the exact numbers in your table may differ from those shown in the lecture.

---

# 3. Checking the Data

Go to **Table View** to see the generated data.

The table now contains:

* `Date`
* `Profit`

Make sure the `Date` column has the appropriate **Date** data type.

### To change the data type

1. Select the `Date` column.
2. Go to the **Data Type** option.
3. Select **Date**.

Then return to **Report View**.

---

# 4. Understanding Cumulative Totals

Before using the DAX functions, understand what a cumulative total means.

Suppose we have:

| Date  | Profit |
| ----- | -----: |
| 1 Jan |     $1 |
| 2 Jan |     $2 |
| 3 Jan |     $4 |

The cumulative values would be:

| Date  | Profit | Cumulative |
| ----- | -----: | ---------: |
| 1 Jan |     $1 |         $1 |
| 2 Jan |     $2 |         $3 |
| 3 Jan |     $4 |         $7 |

### Calculation

For 1 January:

```text
$1
```

For 2 January:

```text
$1 + $2 = $3
```

For 3 January:

```text
$1 + $2 + $4 = $7
```

The important point is that the cumulative total **keeps adding values from the beginning of the relevant time period**.

The relevant time period depends on the function being used:

| Function     | Cumulative period |
| ------------ | ----------------- |
| `TOTALMTD()` | Current month     |
| `TOTALQTD()` | Current quarter   |
| `TOTALYTD()` | Current year      |

---

# 5. TOTALMTD() — Month-to-Date

## What is TOTALMTD?

`TOTALMTD()` calculates the cumulative total **from the beginning of the current month up to the current date**.

### Syntax

```DAX
TOTALMTD(
    <expression>,
    <dates>
)
```

It can also accept an optional filter argument.

### Parameters

**Expression**

The calculation you want to perform.

**Dates**

The date column used to determine the time period.

**Filters**

Optional filters can also be provided.

---

# 6. Creating the TOTALMTD Measure

### Steps

1. Right-click **Calendar Table**.
2. Select **New Measure**.
3. Name the measure `Total MTD`.
4. Use the `TOTALMTD()` function.
5. Calculate the sum of the `Profit` column.
6. Provide the Calendar Table's `Date` column.

### DAX

```DAX
Total MTD =
TOTALMTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

The lecture uses IntelliSense to select the functions and columns.

### Using IntelliSense

While writing DAX:

1. Type a few characters, such as `TOTALMTD`.
2. Power BI displays matching functions.
3. Select the desired function.
4. Press **Tab** to autocomplete it.
5. Type the expression.
6. Add the comma.
7. Select the date column.
8. Close the brackets.
9. Press **Enter**.

---

# 7. Creating a Table Visual for TOTALMTD

To see the result:

1. Click somewhere on the report canvas.
2. Insert a **Table** visual.
3. Add the `Date` field.
4. Add the `Profit` field.
5. Add the `Total MTD` measure.

The table will show the profit for each date and its corresponding month-to-date cumulative value.

---

# 8. How TOTALMTD Works

Suppose January contains:

| Date  | Profit | Total MTD |
| ----- | -----: | --------: |
| 1 Jan |      6 |         6 |
| 2 Jan |      7 |        13 |
| 3 Jan |      4 |        17 |
| 4 Jan |      5 |        22 |

### 1 January

```text
Total MTD = 6
```

Only 1 January has occurred.

### 2 January

```text
6 + 7 = 13
```

### 3 January

```text
6 + 7 + 4 = 17
```

Thus, the cumulative total continues increasing throughout January.

---

# 9. TOTALMTD Resets at the Beginning of Every Month

This is one of the most important concepts.

Suppose:

**31 January = $175**

That means:

```text
Total profit for January = $175
```

When we move to **1 February**, the calculation starts again.

Suppose:

| Date   | Profit | Total MTD |
| ------ | -----: | --------: |
| 31 Jan |    ... |       175 |
| 1 Feb  |      8 |         8 |
| 2 Feb  |      8 |        16 |
| 3 Feb  |      7 |        23 |

For 1 February:

```text
Total MTD = 8
```

For 2 February:

```text
8 + 8 = 16
```

For 3 February:

```text
8 + 8 + 7 = 23
```

It **does not** continue from January's $175.

### Key point

> `TOTALMTD()` resets its cumulative calculation at the beginning of every month.

---

# 10. TOTALQTD() — Quarter-to-Date

Now suppose the requirement changes.

Instead of calculating cumulative totals **within each month**, we want cumulative totals **within each quarter**.

For a standard calendar year, there are four quarters:

| Quarter | Months                      |
| ------- | --------------------------- |
| Q1      | January, February, March    |
| Q2      | April, May, June            |
| Q3      | July, August, September     |
| Q4      | October, November, December |

Therefore:

```text
Q1 → Jan + Feb + Mar
Q2 → Apr + May + Jun
Q3 → Jul + Aug + Sep
Q4 → Oct + Nov + Dec
```

---

# 11. Creating the TOTALQTD Measure

### Steps

1. Right-click **Calendar Table**.
2. Select **New Measure**.
3. Name it `Total QTD`.
4. Use the `TOTALQTD()` function.
5. Calculate the sum of Profit.
6. Provide the Calendar Table's Date column.

### DAX

```DAX
Total QTD =
TOTALQTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

---

# 12. Testing TOTALQTD

Remove the `Total MTD` measure from the table visual and add `Total QTD`.

Suppose:

| Date  | Profit | Total QTD |
| ----- | -----: | --------: |
| 1 Jan |      6 |         6 |
| 2 Jan |      4 |        10 |
| 3 Jan |      4 |        14 |

The calculation is:

### 1 January

```text
6
```

### 2 January

```text
6 + 4 = 10
```

### 3 January

```text
6 + 4 + 4 = 14
```

The cumulative calculation continues throughout Q1.

---

# 13. TOTALQTD Continues Across Months Within a Quarter

This is an important difference from `TOTALMTD()`.

Suppose Q1 contains:

```text
January
February
March
```

The cumulative calculation does **not reset in February**.

Instead:

```text
January → cumulative
February → continues January + February
March → continues January + February + March
```

Therefore, at the end of March, the Q1 cumulative value represents the entire quarter's profit.

For example:

```text
31 March = $480
```

means:

```text
Total profit for Q1 = $480
```

---

# 14. TOTALQTD Resets at the Beginning of Every Quarter

After Q1 ends:

```text
31 March → Q1 ends
1 April → Q2 begins
```

Therefore, the cumulative calculation starts again.

Suppose:

| Date   | Profit | Total QTD |
| ------ | -----: | --------: |
| 31 Mar |    ... |       480 |
| 1 Apr  |      6 |         6 |
| 2 Apr  |      5 |        11 |

For 1 April:

```text
Total QTD = 6
```

For 2 April:

```text
6 + 5 = 11
```

So `TOTALQTD()` resets at the beginning of each quarter.

---

# 15. TOTALYTD() — Year-to-Date

Now suppose the requirement is to calculate cumulative profit for an **entire calendar year**.

For example:

```text
1 January → 31 December
```

We use:

```DAX
TOTALYTD()
```

### Meaning

`TOTALYTD()` calculates the cumulative value from the beginning of the current year up to the current date.

---

# 16. Creating the TOTALYTD Measure

### Steps

1. Right-click **Calendar Table**.
2. Select **New Measure**.
3. Name it `Total YTD`.
4. Use `TOTALYTD()`.
5. Provide `SUM(Profit)` as the expression.
6. Provide the Calendar Table's `Date` column.

### DAX

```DAX
Total YTD =
TOTALYTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

---

# 17. Understanding TOTALYTD

Suppose the beginning of 2022 contains:

| Date       | Profit | Total YTD |
| ---------- | -----: | --------: |
| 1 Jan 2022 |      3 |         3 |
| 2 Jan 2022 |      2 |         5 |
| 3 Jan 2022 |      9 |        14 |

### 1 January

```text
3
```

### 2 January

```text
3 + 2 = 5
```

### 3 January

```text
3 + 2 + 9 = 14
```

The cumulative total continues throughout the entire year.

---

# 18. TOTALYTD Resets at the Beginning of a New Year

Suppose the total for 2022 on:

**31 December 2022 = $1,937**

This represents the total profit for the entire year 2022.

When we move to:

**1 January 2023**

the calculation starts again.

Suppose:

| Date        | Profit | Total YTD |
| ----------- | -----: | --------: |
| 31 Dec 2022 |    ... |     1,937 |
| 1 Jan 2023  |      9 |         9 |
| 2 Jan 2023  |     10 |        19 |

For 1 January 2023:

```text
Total YTD = 9
```

For 2 January 2023:

```text
9 + 10 = 19
```

The 2022 value of `$1,937` is not included in the 2023 calculation.

---

# 19. Yearly Example

According to the lecture, for 2023:

```text
1 Jan 2023 → Total YTD = $9
2 Jan 2023 → Total YTD = $19
...
31 Dec 2023 → Total YTD = $2,144
```

Therefore:

```text
Total profit for 2023 = $2,144
```

The calculation then resets again when 2024 starts.

---

# 20. Comparison of MTD, QTD and YTD

This is the most important summary of the lecture.

| Function     | Full Form       | Cumulative Calculation Starts From | Resets At                  |
| ------------ | --------------- | ---------------------------------- | -------------------------- |
| `TOTALMTD()` | Month-to-Date   | Beginning of current month         | Beginning of every month   |
| `TOTALQTD()` | Quarter-to-Date | Beginning of current quarter       | Beginning of every quarter |
| `TOTALYTD()` | Year-to-Date    | Beginning of current year          | Beginning of every year    |

### Example

Suppose today's date is:

**15 May 2025**

Then:

```text
TOTALMTD()
→ 1 May 2025 → 15 May 2025

TOTALQTD()
→ 1 April 2025 → 15 May 2025

TOTALYTD()
→ 1 January 2025 → 15 May 2025
```

This is the easiest way to remember these functions.

---

# 21. Understanding the Total Row in the Table Visual

The lecture also explains an important behavior that can sometimes be confusing.

After adding `Total YTD`, the bottom of the table may show values that aren't simply the sum of all the displayed YTD values.

For example, the lecture observes:

```text
1895
```

This represents the cumulative YTD value for the **latest year available**, which is 2025.

It does **not** mean that all yearly YTD values have been added together.

---

# 22. Total Row with TOTALMTD

When the `Total MTD` column is selected, the bottom value was:

```text
158
```

This represents the total for the **latest month available**.

The dataset ends in:

```text
December 2025
```

Therefore:

```text
158 = Total profit for December 2025
```

It is not the sum of all MTD values across every month.

The overall profit across the entire dataset remains:

```text
$8,045
```

---

# 23. Total Row with TOTALQTD

When `Total QTD` is selected, the bottom value is:

```text
458
```

The latest available quarter is:

```text
Q4 2025
```

Q4 consists of:

```text
October 2025
November 2025
December 2025
```

Therefore:

```text
458 = Total profit for Q4 2025
```

Again, this is not the cumulative total across every quarter in the dataset.

The total profit across the entire dataset remains:

```text
$8,045
```

---

# 24. Important Concept: Measure Totals Are Context-Dependent

The lecture demonstrates an important Power BI/DAX concept:

> The total shown for a measure is not necessarily the arithmetic sum of the values visible above it.

For example, with `TOTALMTD()`, the total row can represent the MTD result for the **latest month in the current filter context**.

Similarly:

* `TOTALMTD()` → latest month's total
* `TOTALQTD()` → latest quarter's total
* `TOTALYTD()` → latest year's total

while the ordinary `SUM(Profit)` gives the total across the entire available dataset.

---

# 25. Important DAX Measures from the Lecture

### Calendar Table

```DAX
Calendar Table =
CALENDAR(
    DATE(2022, 1, 1),
    DATE(2025, 12, 31)
)
```

### Profit Column

```DAX
Profit = RANDBETWEEN(1, 10)
```

### Total MTD

```DAX
Total MTD =
TOTALMTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

### Total QTD

```DAX
Total QTD =
TOTALQTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

### Total YTD

```DAX
Total YTD =
TOTALYTD(
    SUM('Calendar Table'[Profit]),
    'Calendar Table'[Date]
)
```

---

# 26. How to Decide Which Function to Use?

Use the function based on **where you want the cumulative calculation to restart**.

### Need cumulative total for each month?

Use:

```DAX
TOTALMTD()
```

Example:

```text
1 Jan → cumulative
2 Jan → cumulative
...
31 Jan → total January

1 Feb → starts again
```

---

### Need cumulative total for each quarter?

Use:

```DAX
TOTALQTD()
```

Example:

```text
Jan → cumulative
Feb → cumulative
Mar → total Q1

Apr → starts again
```

---

### Need cumulative total for each year?

Use:

```DAX
TOTALYTD()
```

Example:

```text
Jan → cumulative
Feb → cumulative
...
Dec → total year

Next January → starts again
```

---

# 27. Overall Flow of the Demonstration

The complete process followed in the lecture is:

```text
Open Power BI Desktop
        ↓
Create Blank Report
        ↓
Modeling → New Table
        ↓
Create Calendar Table using CALENDAR()
        ↓
Create Profit calculated column
        ↓
Use RANDBETWEEN(1,10)
        ↓
Check data in Table View
        ↓
Ensure Date has Date data type
        ↓
Return to Report View
        ↓
Create Total MTD measure
        ↓
Add Date + Profit + Total MTD to Table
        ↓
Observe monthly cumulative values
        ↓
Create Total QTD measure
        ↓
Replace Total MTD with Total QTD
        ↓
Observe quarterly cumulative values
        ↓
Create Total YTD measure
        ↓
Replace Total QTD with Total YTD
        ↓
Observe yearly cumulative values
```

---

# 28. Key Takeaways

### `TOTALMTD()`

* Means **Month-to-Date**.
* Calculates cumulative values from the beginning of the current month.
* Resets when a new month begins.
* Example: February doesn't include January's values.

### `TOTALQTD()`

* Means **Quarter-to-Date**.
* Calculates cumulative values from the beginning of the current quarter.
* Resets when a new quarter begins.
* Q1 = January–March.
* Q2 = April–June.
* Q3 = July–September.
* Q4 = October–December.

### `TOTALYTD()`

* Means **Year-to-Date**.
* Calculates cumulative values from the beginning of the current year.
* Resets when a new year begins.
* For a calendar year, the period is January 1–December 31.

### Most important memory trick

> **MTD → Month reset**
> **QTD → Quarter reset**
> **YTD → Year reset**

And all three follow the same basic pattern:

```text
Current Date
     ↑
Cumulative calculation
     ↑
Beginning of relevant period
```

So the only major difference is **where the cumulative calculation starts**.
