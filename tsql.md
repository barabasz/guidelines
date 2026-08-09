# TSQL: Transact-SQL (SQL Server / Azure SQL)

## Table of Contents

- [NAMING](#naming)
- [STRUCTURE](#structure)
- [FORMATTING](#formatting)
- [TABLE](#table)
- [TYPE](#type)
- [CONSTRAINT](#constraint)
- [INDEX](#index)
- [VIEW](#view)

## NAMING

### TSQL-NAMING-010: ASCII-only identifiers

* Requirement: **MUST**

Identifiers MUST use only ASCII letters, digits, and the underscore (`_`). Polish diacritics, spaces, and hyphens MUST NOT appear in any identifier.

#### Rationale
Non-ASCII characters and characters that require a delimiter (spaces, hyphens) force the use of bracketed identifiers everywhere they are referenced, which hurts portability and readability (see TSQL-NAMING-040).

#### Good Example
```sql
CREATE TABLE shop.order_items ( ... );
```

#### Bad Example
```sql
CREATE TABLE shop.[pozycje-zamówienia] ( ... );
```

### TSQL-NAMING-020: snake_case for all identifiers

* Requirement: **MUST**

All identifiers — databases, schemas, tables, columns, constraints, and indexes — MUST use `snake_case`.

#### Good Example
```sql
order_items, user_id, created_at
```

#### Bad Example
```sql
OrderItems, UserID, CreatedAt
```

### TSQL-NAMING-030: Consistent singular/plural form

* Requirement: **MUST**

The choice between singular and plural identifier forms MUST be applied consistently across the project; mixing both forms for the same kind of object is not permitted.

#### Rationale
A consistent form removes a class of naming decisions and makes generated or templated DDL predictable. See TSQL-TABLE-020 for the specific convention used for table names.

### TSQL-NAMING-040: Avoid delimited identifiers

* Requirement: **SHOULD NOT**

Bracket-delimited identifiers (`[ ]`) SHOULD NOT be used unless required to avoid a collision with a reserved SQL keyword. A name that needs no delimiter is more readable and more portable across tools.

#### Good Example
```sql
SELECT user_id FROM shop.users;
```

#### Bad Example
```sql
SELECT [user_id] FROM shop.[users];
```

### TSQL-NAMING-050: Limit abbreviations

* Requirement: **SHOULD**

Abbreviations SHOULD be avoided except for a small, explicitly agreed set (e.g. `id`, `qty`) that is applied consistently across the project.

## STRUCTURE

### TSQL-STRUCTURE-010: Business logic belongs in a dedicated schema

* Requirement: **MUST**

Business logic MUST be organized into a dedicated schema rather than left in `dbo`. A schema acts as both a namespace and a responsibility/topic boundary (e.g. `shop`, `pilot`, `dict`).

#### Good Example
```sql
CREATE SCHEMA shop;
GO
CREATE TABLE shop.users ( ... );
```

#### Bad Example
```sql
CREATE TABLE dbo.users ( ... );
```

## FORMATTING

### TSQL-FORMATTING-010: One GO after database/schema statements

* Requirement: **MUST**

A single `GO` batch separator MUST follow each `CREATE DATABASE`, `CREATE SCHEMA`, or `USE` statement.

#### Good Example
```sql
CREATE DATABASE shop_demo;
GO
USE shop_demo;
GO
CREATE SCHEMA shop;
GO
```

## TABLE

### TSQL-TABLE-010: Surrogate primary key

* Requirement: **MUST**

Every table MUST have a surrogate primary key — `INT IDENTITY(1,1)`, or `BIGINT IDENTITY(1,1)` when large volume is expected — named `<table_name_singular>_id`.

#### Good Example
```sql
CREATE TABLE shop.users (
    user_id INT IDENTITY(1,1) NOT NULL,
    CONSTRAINT PK_users PRIMARY KEY CLUSTERED (user_id)
);
```

### TSQL-TABLE-020: Table names MUST be plural

* Requirement: **MUST**

Table names MUST use plural nouns (e.g. `users`, `orders`, `order_items`).

#### Rationale
This is the concrete, project-wide instantiation of the general consistency principle in TSQL-NAMING-030.

### TSQL-TABLE-030: Natural keys MUST NOT be the primary key

* Requirement: **MUST NOT**

A natural key (e.g. `email`) MUST NOT be used as the primary key. The surrogate key defined in TSQL-TABLE-010 remains the PK.

### TSQL-TABLE-040: Natural keys SHOULD be enforced with UNIQUE

* Requirement: **SHOULD**

A natural key SHOULD be enforced with a `UNIQUE` constraint.

#### Good Example
```sql
CONSTRAINT UQ_users_email UNIQUE (email)
```

### TSQL-TABLE-050: Required created_at column

* Requirement: **MUST**

Every table MUST include a `created_at` column of type `DATETIME2(3)` with `DEFAULT SYSUTCDATETIME()`.

#### Good Example
```sql
created_at DATETIME2(3) NOT NULL CONSTRAINT DF_users_created_at DEFAULT SYSUTCDATETIME()
```

### TSQL-TABLE-060: Tracking the last modification

* Requirement: **MUST**

The timestamp of a row's last modification MUST be tracked using exactly one mechanism, appropriate to the table type: an `updated_at` column maintained by an `AFTER UPDATE` trigger for an ordinary table, or the native period column for a system-versioned (temporal) table (see TSQL-TABLE-080).

#### Rationale
A temporal table's period column (`SysStartTime`) already records when a row last changed; adding `updated_at` on top of it duplicates the same information through two different mechanisms.

#### Bad Example
```sql
CREATE TABLE shop.users (
    user_id       INT IDENTITY(1,1) NOT NULL,
    email         NVARCHAR(254) NOT NULL,
    updated_at    DATETIME2(3) NULL,                             -- redundant
    SysStartTime  DATETIME2 GENERATED ALWAYS AS ROW START NOT NULL,
    SysEndTime    DATETIME2 GENERATED ALWAYS AS ROW END NOT NULL,
    PERIOD FOR SYSTEM_TIME (SysStartTime, SysEndTime),
    CONSTRAINT PK_users PRIMARY KEY CLUSTERED (user_id)
)
WITH (SYSTEM_VERSIONING = ON);
```

### TSQL-TABLE-070: No high-churn technical columns in business tables

* Requirement: **MUST NOT**

High-churn, purely technical columns (e.g. `last_login_at`, session counters, session status) MUST NOT be added to a table that carries business history. Track them in a separate append-only table instead.

### TSQL-TABLE-080: When to use temporal tables

* Requirement: **SHOULD**
* Scope: System-versioned (temporal) tables

System-versioned (temporal) tables SHOULD be used where change over time has business, analytical, or audit value (e.g. a status lifecycle).

#### Rationale
A temporal table records *what* changed and *when*, but not *who* made the change or *why* — it is not a complete audit trail without additional columns or mechanisms.

### TSQL-TABLE-090: When not to use temporal tables

* Requirement: **SHOULD NOT**
* Scope: System-versioned (temporal) tables

Temporal tables SHOULD NOT be used for columns that change frequently with no lasting value, since this clutters the history table with noise.

### TSQL-TABLE-100: Deliberate history retention

* Requirement: **MUST**
* Scope: System-versioned (temporal) tables

The `HISTORY_RETENTION_PERIOD` of a temporal table MUST be set deliberately rather than left unlimited.

#### Good Example
```sql
WITH (
    SYSTEM_VERSIONING = ON (
        HISTORY_TABLE = shop.users_history,
        HISTORY_RETENTION_PERIOD = 5 YEARS
    )
)
```

## TYPE

### TSQL-TYPE-010: Deliberate NVARCHAR length

* Requirement: **MUST**

Character column lengths MUST be chosen deliberately, with a justified limit. `NVARCHAR(MAX)` MUST NOT be used as an unreasoned default.

#### Good Example
```sql
email NVARCHAR(254)   -- RFC 5321 maximum mailbox length
```

#### Bad Example
```sql
email NVARCHAR(MAX)
```

### TSQL-TYPE-020: DECIMAL for money, never FLOAT/REAL

* Requirement: **MUST**

Monetary amounts and prices MUST use `DECIMAL(p,s)`. `FLOAT` and `REAL` MUST NOT be used for monetary values.

#### Rationale
`FLOAT`/`REAL` are binary floating-point types and cannot represent decimal fractions exactly, which introduces rounding errors in financial calculations.

## CONSTRAINT

### TSQL-CONSTRAINT-010: Explicit constraint names

* Requirement: **MUST**

Every constraint MUST have an explicit name (`CONSTRAINT name ...`). SQL Server's auto-generated names MUST NOT be relied on — they are unreadable and not deterministic across environments.

### TSQL-CONSTRAINT-020: Constraints defined separately from columns

* Requirement: **MUST**

`PRIMARY KEY`, `UNIQUE`, `FOREIGN KEY`, and `CHECK` constraints MUST be defined separately, below the column definitions — never inline on a column, even where the syntax would allow it.

* Exceptions: `NOT NULL` always stays inline, since it is not itself a named `CONSTRAINT`. A `DEFAULT` constraint MAY stay inline next to its column — unlike the other constraint types, it is always single-column and tied to exactly one column definition.

#### Good Example
```sql
CREATE TABLE shop.order_items (
    order_item_id INT IDENTITY(1,1) NOT NULL,
    order_id      INT NOT NULL,
    quantity      INT NOT NULL,

    CONSTRAINT PK_order_items PRIMARY KEY CLUSTERED (order_item_id),
    CONSTRAINT FK_order_items_orders FOREIGN KEY (order_id)
        REFERENCES shop.orders (order_id),
    CONSTRAINT CK_order_items_quantity CHECK (quantity > 0)
);
```

#### Bad Example
```sql
CREATE TABLE shop.order_items (
    order_item_id INT IDENTITY(1,1) PRIMARY KEY,       -- inline, unnamed
    order_id      INT REFERENCES shop.orders(order_id),
    quantity      INT CHECK (quantity > 0)
);
```

### TSQL-CONSTRAINT-030: Uppercase constraint prefixes

* Requirement: **MUST**

Constraint names MUST start with one of the following uppercase prefixes. This is the one deliberate exception to the lowercase naming convention in TSQL-NAMING-020.

| Prefix | Constraint type |
|---|---|
| `PK_` | PRIMARY KEY |
| `UQ_` | UNIQUE |
| `FK_` | FOREIGN KEY |
| `CK_` | CHECK |
| `DF_` | DEFAULT |

### TSQL-CONSTRAINT-040: Constraint naming pattern

* Requirement: **MUST**

Constraint names MUST follow the pattern `<PREFIX>_<table>_<column(s)>`.

#### Good Example
```
CK_users_status, FK_order_items_orders
```

### TSQL-CONSTRAINT-050: Deliberate ON DELETE / ON UPDATE

* Requirement: **MUST**

The `ON DELETE` and `ON UPDATE` behavior of a `FOREIGN KEY` MUST be a deliberate decision, never left at the implicit default without consideration. `NO ACTION` is the default choice; `CASCADE` is used only when a child row is genuinely meaningless without its parent (e.g. `order_items` relative to `orders`).

#### Good Example
```sql
CONSTRAINT FK_order_items_orders FOREIGN KEY (order_id)
    REFERENCES shop.orders (order_id)
    ON DELETE CASCADE
```

### TSQL-CONSTRAINT-060: CHECK over BIT for multi-value status

* Requirement: **SHOULD**

A `CHECK` constraint on a status/enum-like column SHOULD be used instead of `BIT` wherever the state has more than two meaningful values, or may grow to more in the future.

## INDEX

### TSQL-INDEX-010: Explicit clustered primary key

* Requirement: **MUST**

The clustering of the primary key MUST be explicit (`PRIMARY KEY CLUSTERED (...)`), never left to the implicit default.

### TSQL-INDEX-020: Index foreign key columns

* Requirement: **SHOULD**

A foreign key column SHOULD get a dedicated `NONCLUSTERED INDEX`, unless it is already the leading column of another index or composite key that covers it.

#### Good Example
```sql
CREATE NONCLUSTERED INDEX IX_orders_user_id ON shop.orders (user_id);
```

### TSQL-INDEX-030: Index naming pattern

* Requirement: **MUST**

Index names MUST follow the pattern `IX_<table>_<column(s)>`.

### TSQL-INDEX-040: No speculative indexes

* Requirement: **SHOULD NOT**

Indexes SHOULD NOT be added speculatively without a demonstrated need — every index carries a write-time cost.

## VIEW

### TSQL-VIEW-010: View naming

* Requirement: **MUST**

View names MUST be noun phrases describing their content, in `snake_case`, without a `vw_` prefix — the schema already signals that the object belongs to the logic layer, making the prefix redundant.

* Exception: A prefix MAY be used when a project explicitly needs to disambiguate views from tables sharing the same schema.

#### Good Example
```
shop.active_orders
```

#### Bad Example
```
shop.vw_active_orders
```

### TSQL-VIEW-020: CREATE OR ALTER over DROP + CREATE

* Requirement: **SHOULD**

`CREATE OR ALTER VIEW` SHOULD be used instead of `DROP VIEW` + `CREATE VIEW`, since it is safer during iterative development — permissions granted on the object are preserved.

#### Good Example
```sql
CREATE OR ALTER VIEW shop.active_orders AS
SELECT order_id, user_id, status
FROM shop.orders
WHERE status <> 'cancelled';
```

### TSQL-VIEW-030: WITH SCHEMABINDING where feasible

* Requirement: **SHOULD**

A view SHOULD be created `WITH SCHEMABINDING` whenever feasible and appropriate. It protects against unnoticed changes to the underlying tables' structure and is a prerequisite for indexed views in the future.

#### Good Example
```sql
CREATE OR ALTER VIEW shop.active_orders
WITH SCHEMABINDING
AS
SELECT order_id, user_id, status
FROM shop.orders
WHERE status <> 'cancelled';
```

### TSQL-VIEW-040: No SELECT * in views

* Requirement: **MUST**

A view MUST explicitly enumerate its output columns in `SELECT`; `SELECT *` MUST NOT be used.

#### Bad Example
```sql
CREATE OR ALTER VIEW shop.active_orders AS
SELECT * FROM shop.orders WHERE status <> 'cancelled';
```

### TSQL-VIEW-050: Document complex view logic

* Requirement: **SHOULD**

Business logic in a view that cannot be easily traced at a glance — e.g. multi-level CTEs — SHOULD be documented with a comment at the top of the view describing its purpose and assumptions, plus a version/changelog for larger views (following the pattern used in `pilot.kontrole`).

#### Good Example
```sql
/*
    View: shop.active_orders
    Purpose:     Orders currently visible to fulfillment staff.
    Assumptions: 'cancelled' and 'draft' orders are excluded.
    Changelog:
      v1 (2026-08-01): Initial version.
*/
CREATE OR ALTER VIEW shop.active_orders AS
...
```

