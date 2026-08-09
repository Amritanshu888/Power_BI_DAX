# DAX Functions

## 1. Creating a Measures Table

The lecture begins by creating a separate table specifically for storing measures.

### Steps

1. Open the **Data pane**.
2. Go to the **Home** tab.
3. Click **Enter Data**.
4. Create a new table.
5. Name the table:
   **`Measures Table 2`**
6. Click **Load**.
7. Right-click on `Measures Table 2`.
8. Select **New Measure**.
9. Use the formula bar to write the DAX expression.

This measures table is then used throughout the lecture to create and organize the different DAX measures. 

---

# 2. AND Function

## Purpose

The **AND** function checks whether **all specified conditions are TRUE**.

It returns:

* `TRUE` → if **every condition** is true.
* `FALSE` → if **even one condition** is false.

### Syntax

```DAX
AND(<condition1>, <condition2>)
```

### Example from the lecture

A measure named `and function` is created:

```DAX
and function =
AND(
    5 > 2,
    7 > 3
)
```

Both conditions are true:

* `5 > 2` → TRUE
* `7 > 3` → TRUE

Therefore:

```text
Output = TRUE
```

The result is displayed using a **Card visual**. 

### Testing AND

The lecture then changes:

```DAX
5 > 2
```

to:

```DAX
5 > 12
```

Now:

* `5 > 12` → FALSE
* `7 > 3` → TRUE

Since **both conditions must be TRUE**, the AND function returns:

```text
FALSE
```

### Key takeaway

> **AND = TRUE only when ALL conditions are TRUE.** 

---

# 3. OR Function

## Purpose

The **OR** function checks whether **at least one condition is TRUE**.

It returns:

* `TRUE` → if **at least one condition** is true.
* `FALSE` → only when **all conditions** are false.

### Syntax

```DAX
OR(<condition1>, <condition2>)
```

### Example

A new measure called `or function` is created:

```DAX
or function =
OR(
    5 > 2,
    7 > 13
)
```

Evaluate the conditions:

* `5 > 2` → TRUE
* `7 > 13` → FALSE

Because the first condition is TRUE, OR returns:

```text
TRUE
```

The lecture demonstrates this using another Card visual. 

### Important concept

Even if one condition is false, OR still returns TRUE as long as another condition is true.

```text
TRUE OR FALSE = TRUE
```

### Key takeaway

> **OR = TRUE when AT LEAST ONE condition is TRUE.**

---

# 4. NOT Function

## Purpose

The **NOT** function reverses the result of a logical expression.

In simple terms:

```text
TRUE  → FALSE
FALSE → TRUE
```

The lecture describes it as reversing the output of the **AND** or **OR** function. 

---

## NOT with AND

The lecture creates:

```DAX
not function =
NOT(
    AND(
        5 > 2,
        7 > 3
    )
)
```

First evaluate AND:

```text
5 > 2 → TRUE
7 > 3 → TRUE
```

Therefore:

```text
AND(TRUE, TRUE) = TRUE
```

Then NOT reverses it:

```text
NOT(TRUE) = FALSE
```

### Final output

```text
FALSE
```



---

## NOT with OR

The lecture also demonstrates that NOT can be used with OR:

```DAX
NOT(
    OR(
        5 > 2,
        7 > 3
    )
)
```

Since both conditions are true:

```text
OR(TRUE, TRUE) = TRUE
```

NOT reverses the result:

```text
NOT(TRUE) = FALSE
```

So the output remains:

```text
FALSE
```



### Key takeaway

> **NOT reverses a logical result.**

---

# 5. IF Function

## Purpose

The **IF** function evaluates a condition and returns one result when the condition is TRUE and another result when it is FALSE.

### Basic structure

```DAX
IF(
    <condition>,
    <value_if_true>,
    <value_if_false>
)
```

The lecture creates a measure called `if function`. 

### Example

```DAX
if function =
IF(
    5 > 2,
    TRUE,
    FALSE
)
```

Evaluate:

```text
5 > 2 → TRUE
```

Therefore:

```text
Output → TRUE
```

The lecture explains that the IF function requires:

1. A condition
2. What to return if the condition is TRUE
3. What to return if the condition is FALSE



---

## IF with Text

The output does not have to be `TRUE` or `FALSE`.

For example:

```DAX
if function =
IF(
    5 > 2,
    "Yes",
    "No"
)
```

Since:

```text
5 > 2 → TRUE
```

the result becomes:

```text
Yes
```

The lecture specifically demonstrates replacing the TRUE/FALSE outputs with `"Yes"` and `"No"`. 

### Key takeaway

IF follows:

```text
IF condition is TRUE  → return first result
IF condition is FALSE → return second result
```

---

# 6. DATEDIFF Function

## Purpose

The **DATEDIFF** function calculates the difference between two dates according to a specified time interval.

The lecture explains that three things are required:

1. Start date
2. End date
3. Interval



### General structure

```DAX
DATEDIFF(
    <start_date>,
    <end_date>,
    <interval>
)
```

---

## Example: Difference in Years

The lecture uses:

* Start date → 1 January 2022
* End date → 1 January 2023
* Interval → Year

The dates are created using the `DATE()` function.

Conceptually:

```DAX
DATEDIFF(
    DATE(2022, 1, 1),
    DATE(2023, 1, 1),
    YEAR
)
```

The difference is:

```text
1 year
```



---

## Changing the Interval to Days

The lecture then changes:

```DAX
YEAR
```

to:

```DAX
DAY
```

The result becomes:

```text
365 days
```



---

## Changing the Interval to Months

The interval is then changed from:

```DAX
DAY
```

to:

```DAX
MONTH
```

The result becomes:

```text
12 months
```



### Important point

The same two dates can produce different numerical results depending on the interval.

| Interval | Result |
| -------- | -----: |
| YEAR     |      1 |
| DAY      |    365 |
| MONTH    |     12 |

---

# 7. SWITCH Function

The lecture then introduces the **SWITCH** function using a practical example. 

## Example Scenario

The lecture opens the **Table view** and uses a table called:

```text
Example
```

The table contains data for months such as:

* January
* February
* March
* April

It contains two important columns:

* **Sales Price**
* **Cost Price**

The requirement is:

> For each month, display the **Cost Price** if Cost Price is available. If Cost Price is not available, display the **Sales Price** instead.

In other words:

```text
IF Cost Price is available
    → show Cost Price

ELSE
    → show Sales Price
```

This logic is implemented using SWITCH. 

---

# 8. SWITCH with TRUE

The lecture uses the following pattern:

```DAX
SWITCH(
    TRUE(),
    <condition1>, <result1>,
    <default_result>
)
```

The important idea is that:

```DAX
TRUE()
```

is used as the expression.

Then conditions are evaluated one by one.

---

## Creating the SWITCH Measure

### Step 1 — Go to Report View

Return from Table view to **Report view**.

### Step 2 — Create a New Measure

Right-click the `Example` table and select:

**New Measure**

### Step 3 — Name the Measure

The lecture initially attempts to name it:

```text
switch function
```

but this name is already in use, so it is changed to:

```text
switch function one
```



---

## SWITCH Logic

The condition checks whether the **sum of Cost Price is greater than zero**.

Conceptually:

```DAX
switch function one =
SWITCH(
    TRUE(),
    SUM(Example[Cost Price]) > 0,
        SUM(Example[Cost Price]),
    SUM(Example[Sales Price])
)
```

The logic is:

```text
IF SUM(Cost Price) > 0
    → return SUM(Cost Price)

OTHERWISE
    → return SUM(Sales Price)
```

This allows the measure to dynamically choose which value should be displayed.



---

# 9. Displaying and Testing the SWITCH Result

The lecture then creates a table visual.

The table contains:

1. Month
2. Sales Price
3. Cost Price
4. `switch function one`



This allows the result of the SWITCH logic to be compared against the original columns.

---

## February

For February:

```text
Cost Price = 25
```

Since Cost Price is available, the SWITCH measure returns:

```text
25
```

So the Cost Price is used.

---

## March

For March:

```text
Cost Price = 102
```

Therefore:

```text
SWITCH result = 102
```

Again, Cost Price is used.

---

## January

For January, Cost Price is not available.

Therefore the SWITCH condition fails, and the measure uses:

```text
Sales Price
```

The lecture shows:

```text
January → 845
```

So:

```text
SWITCH result = 845
```

---

## April

Similarly, Cost Price is not available for April.

Therefore the measure falls back to Sales Price.

The lecture shows:

```text
April → 665
```

So:

```text
SWITCH result = 665
```



---

# 10. Final Understanding of SWITCH

The entire example can be summarized as:

```text
                Cost Price available?
                       |
              ┌────────┴────────┐
             YES                NO
              |                  |
              ↓                  ↓
      Return Cost Price    Return Sales Price
```

Therefore:

| Month    | Cost Price Available? | SWITCH Result |
| -------- | --------------------- | ------------: |
| January  | No                    |           845 |
| February | Yes                   |            25 |
| March    | Yes                   |           102 |
| April    | No                    |           665 |

The exact values shown above are the values demonstrated in the lecture.

---

# 11. Quick Revision — All Functions

| Function     | Purpose                            | Basic Logic                                        |
| ------------ | ---------------------------------- | -------------------------------------------------- |
| **AND**      | Checks multiple conditions         | TRUE only if **all** are TRUE                      |
| **OR**       | Checks multiple conditions         | TRUE if **at least one** is TRUE                   |
| **NOT**      | Reverses logical output            | TRUE → FALSE, FALSE → TRUE                         |
| **IF**       | Conditional result                 | If condition → result 1, else → result 2           |
| **DATEDIFF** | Finds difference between dates     | Start date, End date, Interval                     |
| **SWITCH**   | Chooses result based on conditions | Checks conditions and returns corresponding result |

---

# 12. Most Important Concepts to Remember

### AND

```text
TRUE + TRUE → TRUE
TRUE + FALSE → FALSE
FALSE + FALSE → FALSE
```

**All conditions must be TRUE.**

### OR

```text
TRUE + TRUE → TRUE
TRUE + FALSE → TRUE
FALSE + FALSE → FALSE
```

**At least one condition must be TRUE.**

### NOT

```text
NOT(TRUE)  → FALSE
NOT(FALSE) → TRUE
```

**Reverses the result.**

### IF

```text
IF(condition, value_if_true, value_if_false)
```

**Chooses between two results.**

### DATEDIFF

```text
DATEDIFF(start_date, end_date, interval)
```

**Calculates date difference in a selected interval such as YEAR, DAY, or MONTH.**

### SWITCH

The lecture uses the particularly useful pattern:

```DAX
SWITCH(
    TRUE(),
    condition1, result1,
    condition2, result2,
    default_result
)
```

This is useful when you have **multiple conditional outcomes**, and the lecture demonstrates it for choosing between Cost Price and Sales Price. 

---

## 13. Power BI Workflow Used Throughout the Lecture

The lecture repeatedly follows this workflow:

```text
Create/Select Table
       ↓
Right-click Table
       ↓
New Measure
       ↓
Write DAX Formula
       ↓
Press Enter
       ↓
Go to Report View
       ↓
Create/Select Visual
       ↓
Add Measure to Fields
       ↓
Observe Result
       ↓
Modify Formula to Experiment
```

For the logical functions, the instructor primarily uses **Card visuals** to make the TRUE/FALSE results easy to observe. For the SWITCH example, a **Table visual** is used so that the calculated result can be compared month-by-month with Sales Price and Cost Price. 

### One-line memory trick

> **AND = all, OR = one, NOT = opposite, IF = choose, DATEDIFF = difference, SWITCH = multiple choices.**

These are the complete concepts and demonstrations covered in this lecture, without adding unrelated material beyond what the transcript supports. 
