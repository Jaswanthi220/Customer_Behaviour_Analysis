Theory



Pandas:👉 Pandas is used to easily read, clean, and analyze data.

🗂️ 1. It reads data easily

With pandas, you can open files like:

CSV

Excel

SQL tables

🧹 2. It helps clean messy data

Real-world data is messy:

Missing values

Duplicates

Wrong formats

📊 3. It helps analyze data quickly

🔍 4. It lets you explore data easily--> info,describe

⚡ 5. It saves time (big reason!)

Tasks that would take:

50–100 lines in pure Python

hours in Excel

Pandas does in 2–3 lines of code.

In one line->Pandas is a Python library used for data manipulation and analysis that makes working with structured data fast, easy, and efficient.”





Difference b/w info and describe

df.info() is used to understand the structure and data types of the dataset, while df.describe() provides statistical summaries of numerical columns.





what is this info and describe all called as?

👉 info() and describe() are called EDA functions (Exploratory Data Analysis methods).

EDA means: Understanding the data before doing analysis or modeling.

EDA helps us detect data quality issues and understand patterns before making business or ML decisions.



-> Df.describe()

“df.describe(include='all') provides summary statistics for both numeric and categorical columns, giving a complete overview of the dataset.”



-> To fill null values with mean or median what to use?

“We choose mean or median based on the distribution of data. If the data is symmetric without outliers, mean is appropriate. If the data is skewed or contains outliers, median is preferred because it is more robust. In our project, we used category-wise median to better represent customer behavior across different segments.”

Mean ≈ Median → symmetric → mean ok

Mean >> Median → right-skewed → median better

🔹 What is skew?

&nbsp;   Is my data leaning left, right, or balanced?



“In notebook-based environments like Jupyter or Colab, the runtime is session-based. If the kernel restarts or cells are run out of order, previously loaded data is lost, which can lead to FileNotFound errors.”





Future Engineering

Creating new variables (features) from raw data that make analysis or modeling easier and more meaningful.



qcut-:  “I used pd.qcut to bin the age variable into quantile-based segments, ensuring balanced customer groups for fair comparison in behavioral analysis.”

pd.cut divides data into equal ranges, whereas pd.qcut divides data into equal-sized quantile groups. I used qcut for customer segmentation to ensure balanced analysis across age groups.





For Deleating rows and columns in pandas we used..

axis=0 → rows → records

axis=1 → columns → features





CTE = Common Table Expression

👉 A CTE is a temporary, named result set that exists only for the duration of the query.

Think of it as:

a temporary table

or a saved sub-query with a name





Business question (plain English)

For each category, which 3 products are purchased the most?

So if we have:

Category = Shoes → top 3 shoes

Category = Clothing → top 3 clothing items

and so on.

The final, clean query we’re explaining

WITH item\_counts AS (

&nbsp;   SELECT

&nbsp;       category,

&nbsp;       item\_purchased,

&nbsp;       COUNT(customer\_id) AS total\_orders

&nbsp;   FROM customer

&nbsp;   GROUP BY category, item\_purchased

),

ranked\_items AS (

&nbsp;   SELECT

&nbsp;       category,

&nbsp;       item\_purchased,

&nbsp;       total\_orders,

&nbsp;       ROW\_NUMBER() OVER (

&nbsp;           PARTITION BY category

&nbsp;           ORDER BY total\_orders DESC

&nbsp;       ) AS item\_rank

&nbsp;   FROM item\_counts

)

SELECT

&nbsp;   category,

&nbsp;   item\_purchased,

&nbsp;   total\_orders,

&nbsp;   item\_rank

FROM ranked\_items

WHERE item\_rank <= 3;



Step 1️⃣: Count how many times each item was purchased

(item\_counts CTE)

SELECT

&nbsp;   category,

&nbsp;   item\_purchased,

&nbsp;   COUNT(customer\_id) AS total\_orders

FROM customer

GROUP BY category, item\_purchased;

What this does

Groups data by category + item

Counts how many times each item was purchased

Output looks like this

category   | item\_purchased | total\_orders

-----------|---------------|-------------

Shoes      | Sneakers       | 120

Shoes      | Boots          | 95

Shoes      | Sandals        | 60

Clothing   | T-shirt        | 200

Clothing   | Jeans          | 150





At this point:

✔ We know totals,

❌ but we don’t know which are top 3 per category yet.



Step 2️⃣: Rank items within each category

(ranked\_items CTE)

ROW\_NUMBER() OVER (

&nbsp;   PARTITION BY category

&nbsp;   ORDER BY total\_orders DESC

) AS item\_rank



What this means (very important 🔑)

PARTITION BY category

👉 Reset ranking for each category

So:

Shoes get their own ranking

Clothing gets a separate ranking

ORDER BY total\_orders DESC

👉 Most purchased item gets rank 1



Example ranking result

category | item\_purchased | total\_orders | item\_rank

---------|---------------|--------------|----------

Shoes    | Sneakers       | 120          | 1

Shoes    | Boots          | 95           | 2

Shoes    | Sandals        | 60           | 3

Shoes    | Flip-flops     | 30           | 4



Clothing | T-shirt        | 200          | 1

Clothing | Jeans          | 150          | 2

Clothing | Jacket         | 80           | 3

Clothing | Shorts         | 40           | 4



Now ranking exists inside each category.



Step 3️⃣: Keep only top 3 per category

WHERE item\_rank <= 3;



✔ Removes ranks 4, 5, …

✔ Keeps top 3 products per category



Final output

category | item\_purchased | total\_orders | item\_rank

---------|---------------|--------------|----------

Shoes    | Sneakers       | 120          | 1

Shoes    | Boots          | 95           | 2

Shoes    | Sandals        | 60           | 3



Clothing | T-shirt        | 200          | 1

Clothing | Jeans          | 150          | 2

Clothing | Jacket         | 80           | 3



Now the key question 🔥

❓ Why ONLY ROW\_NUMBER()?

Because the question says:

Top 3 most purchased products

We want:

Exactly 3 items per category

No ambiguity

ROW\_NUMBER() guarantees:

One unique rank per row

Exactly 3 rows when you filter <= 3

What about other ranking functions?

RANK()

Skips numbers if there’s a tie

Example: 1, 2, 2, 4

❌ Might return more or fewer than 3 items

DENSE\_RANK()

Same rank for ties, no skipping

Example: 1, 2, 2, 3

❌ Might return more than 3 items

ROW\_NUMBER() (used here)

Unique ranking: 1, 2, 3, 4

✔ Always exactly 3 rows

Interview one-liner 🧠

We use ROW\_NUMBER() to ensure exactly three top products per category, even when purchase counts are tied.

**What is a Window Function?** 

👉 A window function performs a calculation across a set of rows related to the current row,

without reducing the number of rows.

*Syntax pattern:*

*FUNCTION\_NAME() OVER (*

    *PARTITION BY ...*

    *ORDER BY ...*

*)*



Example:

ROW\_NUMBER() OVER (PARTITION BY category ORDER BY total\_orders DESC)

Key properties of window functions:

✔ Uses OVER()

✔ Does NOT collapse rows (unlike GROUP BY)

✔ Works on a “window” of rows




**What is a CTE?** 

👉 A CTE (Common Table Expression) is just:

a temporary named query used to organize SQL logic

*Syntax:*

*WITH cte\_name AS (*

    *SELECT ...*

*)*

*SELECT ...*

*FROM cte\_name;*



CTE:

❌ is NOT a window function

❌ does NOT calculate rankings

✔ just stores intermediate results




**One-line interview answer 💼**

Window functions are analytical functions that operate over a defined set of rows using the OVER clause, and CTEs are used to structure queries where such logic is applied in steps.


**Group by V/s Window functions**

GROUP BY → collapses rows ❌
---



Example data (customer table)

category   item        customer\_id

----------------------------------

Shoes      Sneakers    101

Shoes      Sneakers    102

Shoes      Boots       103

Shoes      Boots       104

Shoes      Boots       105



*Query with GROUP BY*

*SELECT category, item, COUNT(\*) AS total\_orders*

*FROM customer*

*GROUP BY category, item;*



Result

category | item     | total\_orders

---------|----------|--------------

Shoes    | Sneakers | 2

Shoes    | Boots    | 3



What happened?



5 rows ➜ 2 rows



Individual customer rows are gone



Rows were collapsed into groups



👉 Once collapsed, you cannot see individual records anymore.



**Window function → does NOT collapse rows ✅**

Same data, now use COUNT() as a window function

*SELECT*

  *category,*

  *item,*

  *customer\_id,*

  *COUNT(\*) OVER (PARTITION BY category, item) AS total\_orders*

*FROM customer;*



Result

category | item     | customer\_id | total\_orders

---------|----------|-------------|--------------

Shoes    | Sneakers | 101         | 2

Shoes    | Sneakers | 102         | 2

Shoes    | Boots    | 103         | 3

Shoes    | Boots    | 104         | 3

Shoes    | Boots    | 105         | 3



What happened?

All original rows are still there

Aggregate value is attached to each row

No row was removed or merged

ROW\_NUMBER vs RANK vs DENSE\_RANK with real examples
---


Imagine this is items in one category ordered by total sales:



item        total\_orders

------------------------

Hat         100

Shoes       90

Bag         90

Belt        80

Socks       70



Notice:

Shoes and Bag are tied (90)



1️⃣ ROW\_NUMBER() — unique numbering (no ties)

ROW\_NUMBER() OVER (ORDER BY total\_orders DESC)



Result

item   | total\_orders | row\_number

-------|--------------|-----------

Hat    | 100          | 1

Shoes  | 90           | 2

Bag    | 90           | 3

Belt   | 80           | 4

Socks  | 70           | 5



Key behavior

✔ Every row gets a unique number

❌ Ties are forced to break arbitrarily

Use when

You want exactly N rows

Ties don’t matter

👉 Top 3 items → use ROW\_NUMBER()

2️⃣ RANK() — skips numbers on ties

RANK() OVER (ORDER BY total\_orders DESC)



Result

item   | total\_orders | rank

-------|--------------|------

Hat    | 100          | 1

Shoes  | 90           | 2

Bag    | 90           | 2

Belt   | 80           | 4

Socks  | 70           | 5



What changed?

Shoes \& Bag share rank 2

Rank 3 is skipped

Use when

Ties matter

Rank position must reflect order gaps

👉 Sports rankings, competition ranks



3️⃣ DENSE\_RANK() — no gaps in ranking

DENSE\_RANK() OVER (ORDER BY total\_orders DESC)



Result

item   | total\_orders | dense\_rank

-------|--------------|-----------

Hat    | 100          | 1

Shoes  | 90           | 2

Bag    | 90           | 2

Belt   | 80           | 3

Socks  | 70           | 4



What changed?

Same ties as RANK()

No skipped numbers

Use when

You want continuous ranks

Logical grouping matters


Number of Customers = COUNT('public customer'\[customer\_id])






