# DAX Query Evaluation

## 1. What Happens When You Write a DAX Query?

Whenever you write a **DAX query or DAX expression**, it needs to be processed by the Power BI engine before the final result can be returned.

Behind the scenes, two major engines are involved:

1. **Formula Engine (FE)**
2. **Storage Engine (SE)**

The overall flow is:

```text
DAX Query / Expression
          ↓
    Formula Engine
          ↓
      Query Plan
          ↓
    Storage Engine
          ↓
      Data Cache
          ↓
    Formula Engine
          ↓
      Final Result
```

Understanding this flow helps explain how DAX calculations are actually executed internally.

---

# 2. The Two Engines

## A. Formula Engine (FE)

The **Formula Engine** is responsible for processing the DAX expression and determining **how the requested calculation should be performed**.

It is the first engine that receives the DAX query.

### Main responsibilities

The Formula Engine:

* Receives the DAX query/expression.
* Processes the DAX expression.
* Creates a **query plan**.
* Sends the required operations to the Storage Engine.
* Receives the resulting **data cache**.
* Performs further calculations against that data cache.
* Produces the final result.

So, conceptually:

> **Formula Engine = Understands and orchestrates the DAX calculation.**

---

# 3. Storage Engine (SE)

The **Storage Engine** is responsible for retrieving the required data based on the instructions/query plan received from the Formula Engine.

It executes the query plan and retrieves the required data.

### Main responsibility

The Storage Engine:

1. Receives instructions from the Formula Engine.
2. Executes the required data retrieval operations.
3. Retrieves the required data.
4. Returns the data to the Formula Engine in the form of a **data cache**.

Conceptually:

> **Storage Engine = Retrieves and processes the required data.**

---

# 4. Complete DAX Evaluation Process

Let's understand the complete process step by step.

## Step 1 — Write a DAX Query

Suppose you write some DAX expression.

For example:

```DAX
Total Sales = SUM(Sales[Sales Amount])
```

The DAX expression is sent to the:

### Formula Engine

---

## Step 2 — Formula Engine Processes the Query

The Formula Engine analyzes the DAX expression.

It determines what needs to happen in order to produce the requested result.

The Formula Engine then creates a:

### Query Plan

---

# 5. What is a Query Plan?

A **query plan** is essentially a set of instructions that describes **how the DAX query should be executed**.

The Formula Engine creates the query plan.

The query plan determines things such as:

* How the data should be retrieved.
* How tables should be joined.
* How data should be filtered.
* How aggregations should be performed.
* What operations need to be executed.
* Which steps are required to produce the requested result.

In simple terms:

> **Query Plan = A detailed execution strategy for the DAX query.**

---

# 6. Logical Operations and Physical Operations

The query plan breaks down the DAX expression into two broad types of operations:

### 1. Logical Operations

### 2. Physical Operations

---

## Logical Operations

Logical operations describe **what needs to be done**.

For example:

```text
Filter the data
Join two tables
Calculate an aggregation
Group data
```

They represent the logical requirements of the query.

---

## Physical Operations

Physical operations describe **how those operations will actually be executed**.

They represent the actual execution steps used by the engine to retrieve and process the data.

Therefore:

```text
Logical Operation
        ↓
"What needs to happen?"
        ↓
Physical Operation
        ↓
"How will it actually happen?"
```

---

# 7. What Does the Query Plan Decide?

The query plan determines several important aspects of query execution.

### A. Data Retrieval

It determines:

> **How should the required data be retrieved?**

---

### B. Joins

It determines:

> **How should joins between tables be applied?**

For example, if information is required from two related tables, the execution plan determines how the required data should be combined.

---

### C. Filtering

It determines:

> **How should the data be filtered?**

For example:

```text
Sales where Year = 2026
```

The query plan determines how this filtering operation should be performed.

---

### D. Aggregation

It determines:

> **How should the data be aggregated?**

Examples of aggregations include:

```text
SUM
COUNT
AVERAGE
MIN
MAX
```

---

# 8. Query Plan → Storage Engine

Once the Formula Engine creates the query plan, it sends the necessary instructions to the:

### Storage Engine

The Storage Engine then executes those instructions and retrieves the required data.

The result of this operation is returned to the Formula Engine as a:

### Data Cache

---

# 9. What is Data Cache?

A **data cache** is a temporary, in-memory representation of the data retrieved by the Storage Engine.

In simple terms:

> **Data Cache = Temporary in-memory table/data returned by the Storage Engine for further processing.**

The data cache is generated in response to the request made by the Formula Engine.

Conceptually:

```text
Formula Engine
      ↓
Query Plan
      ↓
Storage Engine
      ↓
Required Data
      ↓
Data Cache
      ↓
Formula Engine
```

---

# 10. What Does the Formula Engine Do with the Data Cache?

After receiving the data cache from the Storage Engine, the **Formula Engine performs the remaining calculations** required by the DAX expression.

It executes the necessary calculations against the retrieved data.

Finally:

### The Formula Engine produces the final DAX result.

---

# 11. Complete Architecture

The entire process can be represented as:

```text
                DAX Query
                    │
                    ▼
          ┌─────────────────┐
          │ Formula Engine  │
          └─────────────────┘
                    │
                    │ Creates Query Plan
                    ▼
          ┌─────────────────┐
          │   Query Plan    │
          │                 │
          │ Logical Ops     │
          │ Physical Ops    │
          └─────────────────┘
                    │
                    ▼
          ┌─────────────────┐
          │ Storage Engine  │
          └─────────────────┘
                    │
                    │ Retrieves Data
                    ▼
          ┌─────────────────┐
          │   Data Cache    │
          │ (In-Memory Data)│
          └─────────────────┘
                    │
                    ▼
          ┌─────────────────┐
          │ Formula Engine  │
          │                 │
          │ Final Calculations
          └─────────────────┘
                    │
                    ▼
              Final Result
```

---

# 12. Formula Engine vs Storage Engine

| Feature                     | Formula Engine | Storage Engine |
| --------------------------- | -------------- | -------------- |
| Receives DAX query          | ✅              | ❌              |
| Processes DAX expression    | ✅              | ❌              |
| Creates query plan          | ✅              | ❌              |
| Executes data retrieval     | Coordinates    | ✅              |
| Retrieves required data     | ❌              | ✅              |
| Returns data cache          | Receives       | Produces       |
| Performs final calculations | ✅              | ❌              |
| Produces final result       | ✅              | ❌              |

---

# 13. Simple Real-World Analogy

Think of a restaurant.

### You = Customer

You place an order:

> "Give me a chicken pizza."

### Formula Engine = Manager/Chef

The manager understands what needs to be done and creates a plan:

```text
Get ingredients
↓
Prepare dough
↓
Add toppings
↓
Bake pizza
```

### Storage Engine = Kitchen/Storage

It retrieves the required ingredients/data based on the instructions.

```text
Retrieve:
- Dough
- Chicken
- Cheese
- Vegetables
```

These retrieved ingredients are similar to the:

### Data Cache

Then the Formula Engine performs the remaining processing/calculations and produces:

### Final Result → Pizza

This is just an analogy, but it makes the FE → SE → FE flow easier to remember.

---

# 14. Key Concepts to Remember

### Formula Engine

> Processes the DAX expression and creates the query plan.

### Query Plan

> Defines the logical and physical operations required to execute the DAX query.

It determines things such as:

* Data retrieval
* Joins
* Filters
* Aggregations

### Storage Engine

> Executes the required data retrieval operations based on the query plan.

### Data Cache

> Temporary in-memory data returned by the Storage Engine to the Formula Engine.

### Final Result

> The Formula Engine performs the required calculations on the data cache and returns the final DAX result.

---

# ⭐ Quick Revision

Remember the entire process using:

**DAX → FE → Query Plan → SE → Data Cache → FE → Result**

Or:

```text
DAX Query
   ↓
Formula Engine
   ↓
Query Plan
   ↓
Storage Engine
   ↓
Data Cache
   ↓
Formula Engine
   ↓
Final Result
```

### One-line summary:

> **The Formula Engine interprets the DAX query and creates an execution plan, the Storage Engine retrieves the required data and returns a data cache, and the Formula Engine performs the remaining calculations to produce the final result.**

This is the core concept you need to remember from this lecture.
