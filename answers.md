# Advanced Database Modeling – Answers (Q1–Q8)

## Q1. Movie attributes

Using Figure Q5.8, a movie is described by:

**Attributes inherited from `PRODUCT`**  
- `Prod_Num` – product number (PK)  
- `Prod_Title` – title of the product  
- `Prod_ReleaseDate` – release date  
- `Prod_Price` – price  
- `Prod_Type` – discriminator indicating that this product is a `MOVIE`

**Attributes specific to `MOVIE` subtype**  
- `Movie_Rating`  
- `Movie_Director`

So, the full set of attributes that describes a movie is:  
`Prod_Num`, `Prod_Title`, `Prod_ReleaseDate`, `Prod_Price`, `Prod_Type`, `Movie_Rating`, `Movie_Director`.


---

## Q2. PRODUCT–CD relationship

**Question:** Does every row in `PRODUCT` have to be related to a row in `CD`?

**Answer:** No.  
Only products whose type is CD (i.e., `Prod_Type = 'CD'`) will have a corresponding row in the `CD` subtype table.  
Rows in `PRODUCT` that represent books or movies are related instead to the `BOOK` or `MOVIE` subtype tables, not to `CD`.  
Because `PRODUCT` is a supertype with multiple subtypes, each product instance belongs to exactly one of the subtypes (`BOOK`, `CD`, or `MOVIE`), so not every `PRODUCT` row is related to `CD`.


---

## Q3. BOOK without PRODUCT

**Question:** Can a row exist in `BOOK` without a corresponding row in `PRODUCT`?

**Answer:** No.  
In the model, `BOOK.Prod_Num` is both the **primary key** of `BOOK` and a **foreign key** that references `PRODUCT.Prod_Num`.  
This means every book must be a product first, and referential integrity requires that the referenced `PRODUCT` row exists before a `BOOK` row can be inserted.  
Therefore a `BOOK` row cannot exist without a matching `PRODUCT` row.


---

## Q4. Entity cluster

An **entity cluster** is a conceptual grouping of several related entities that are treated as a single “super-entity” in a high-level ER diagram.  
It is mainly a **presentation and simplification tool**: instead of drawing many small entities and relationships, the modeler collapses them into one cluster to reduce visual complexity.  
This makes large or complex ER diagrams easier to read, discuss, and maintain, especially when only the high-level structure is needed (for example, in presentations or early design).  
Internally, the detailed entities and relationships still exist in the logical model; the cluster just hides that detail at the conceptual level.


---

## Q5. Desirable properties of primary keys

Typical desirable characteristics of a primary key are:

- **Uniqueness** – Each key value must identify at most one row.  
  This guarantees that every row can be found reliably and avoids ambiguity when joining tables or referencing rows with foreign keys.

- **Stability (non-volatile)** – The key value should **not change frequently** over time.  
  If a primary key changes often, all related foreign keys must also be updated, which is error-prone and expensive.

- **Minimality (irreducibility)** – The key should contain **no unnecessary attributes**; if one attribute can be removed and uniqueness still holds, the key was not minimal.  
  Minimal keys simplify indexes and make the design easier to understand.

- **Non-null and always present** – Primary key attributes should be defined as `NOT NULL` and always filled.  
  This ensures that every row is identifiable and supports referential integrity from other tables.

- **Simplicity / small size** – Short, simple keys (for example, a single integer column) are more efficient to index and compare than long strings or wide composites.  
  They typically improve performance of joins and searches.

- **Non-intelligent (no business meaning)** – Ideally, the key does not encode business logic such as country codes or year codes.  
  When business rules change, intelligent keys become invalid or misleading, while neutral keys remain valid.


---

## Q6. Composite primary keys

A **composite primary key** uses two or more columns together to uniquely identify a row.  
It is appropriate when no single attribute is unique on its own, but the **combination** of attributes naturally identifies each instance.  

A common example is an associative / bridge entity, such as `ENROLLMENT(Student_ID, Course_ID)` in a university system.  
Here, neither `Student_ID` nor `Course_ID` alone is unique in the `ENROLLMENT` table, but the pair `(Student_ID, Course_ID)` uniquely identifies each enrollment record.  
Using the composite key also makes it clear that the row represents the relationship between those two entities, not a separate object with its own independent identity.


---

## Q7. Surrogate primary keys

A **surrogate primary key** is an artificial, system-generated identifier (for example, an auto-incrementing integer or a UUID) that has no business meaning by itself.  
We choose a surrogate key instead of a natural key when the natural candidate key is long, composite, changes over time, or contains sensitive information (such as an email address).  

**Benefits:**
- Surrogate keys are typically short, stable, and efficient for indexing and joining tables.  
- They decouple the database identity from changing business attributes, reducing the need for cascading updates.

**Potential drawback:**
- The key does not carry any semantic meaning, so you must add additional **unique constraints** on natural attributes to prevent duplicate real-world entities.  
- It can also make debugging harder, because seeing `Order_ID = 4711` is less informative than a meaningful natural key.


---

## Q8. 1:1 relationship and foreign key placement

For a **1:1 relationship** where one side is **mandatory** and the other side is **optional**, a common design is:

- Place the **foreign key in the optional table**, pointing to the primary key of the mandatory table.  

Reasoning:

- The mandatory entity must exist independently, so its table should not depend on the optional entity.  
- The optional entity’s rows represent extra information that may or may not be present for a given mandatory row.  
- Any optional row that does exist must be related to exactly one mandatory row, so its foreign key should be defined as `NOT NULL` (for rows that exist).  
- The “optionality” is modeled by the fact that some mandatory rows simply have **no corresponding row** in the optional table at all.

Example:  
If `PERSON` is mandatory and `PASSPORT` is optional, we put `Person_ID` as a `NOT NULL` foreign key in `PASSPORT`.  
Every passport must belong to a person, but not every person has a passport.

