# Technical Guidelines & Standards

This repository serves as the [Single Source of Truth](https://en.wikipedia.org/wiki/Single_source_of_truth) (SSOT) for technical standards, best practices, and project conventions across software engineering, data management, Business Intelligence (BI), and system configuration. It is designed to be clear, concise, and directly actionable for both human engineers (including me in the first place) and Large Language Models (LLMs / AI assistants).

A full list of available standards is maintained in [TOC.md](TOC.md).

**Disclaimer:** These guidelines are a practical, evolving reference created primarily for my own use, as well as for other interested people and AI assistants. They are not an industry standard, formal specification, or institutional policy, and no long-term semantic stability of individual rules or Rule IDs is guaranteed. The current version of the repository defines the current guidance; Git history preserves how that guidance has evolved over time.

This README defines repository-level conventions for writing and maintaining guideline documents. It is not itself a technology-specific guideline, so the meta-guidelines in this file do not use Rule IDs.

## Table of Contents

- [1. Requirement Levels](#1-requirement-levels)
- [2. Rule Identification Protocol](#2-rule-identification-protocol)
- [3. Guideline Document Structure](#3-guideline-document-structure)
- [4. Standard Rule Structure](#4-standard-rule-structure)
- [5. Meta-Guidelines for Writing Guidelines](#5-meta-guidelines-for-writing-guidelines)

## 1. Requirement Levels

All guidelines use the requirement keywords defined by BCP 14 ([RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) and [RFC 8174](https://datatracker.ietf.org/doc/html/rfc8174)). These words have their normative meaning only when written in ALL CAPS:

* **MUST / MUST NOT**: Absolute requirement or prohibition. A violation makes the implementation non-compliant with the applicable guidelines.
* **SHOULD / SHOULD NOT**: Strong recommendation. A deviation is permitted only when there is a specific, well-understood reason to do so.
* **MAY**: Fully optional. The rule permits, but does not require, the described behavior.

## 2. Rule Identification Protocol

Every technical rule in a guideline document **MUST** have a stable, unique identifier using the following format:

`[TECH]-[CATEGORY]-[NUMBER]`

### Format Specifications

* **TECH**: The canonical uppercase technology identifier defined in the Technology Registry below. When adding a new technology, choose a short, natural, widely recognized, and unambiguous identifier. Its length is not fixed in advance.
* **CATEGORY**: An uppercase word representing the subject area (e.g., `TABLE`, `VIEW`, `NAMING`). It **SHOULD** be short, natural, and clear, and **SHOULD** use the non-plural form (see the Category Registry below). Its length is not fixed in advance. Existing category identifiers **SHOULD** be reused when they accurately represent the same subject; synonymous categories **SHOULD NOT** be introduced without a specific reason.
* **NUMBER**: A 3-digit number unique within each `TECH-CATEGORY` combination. New rules **SHOULD** normally use increments of 10 (`010`, `020`, `030`, ...). Intermediate numbers **MAY** be used when inserting related rules between existing ones.

### Key Principles

1. **No Arbitrary Length Caps**: Neither `TECH` nor `CATEGORY` has a fixed character limit. Natural readability, clarity, and widespread recognition take precedence over rigid length restrictions.
2. **Stable Identity**: Rule IDs identify rules, not document positions. An existing Rule ID **SHOULD** remain associated with the same general subject and **SHOULD NOT** be reused for an unrelated rule.
3. **Removed IDs**: A Rule ID that has been removed **SHOULD NOT** be reused for a different rule. Gaps in numbering have no semantic meaning and do not indicate missing rules.
4. **Independent Numbering**: Numbering is independent for each `TECH-CATEGORY` combination. For example, `SQL-VIEW-010`, `SQL-TABLE-010`, and `PY-TYPE-010` may all exist at the same time.

### Technology Registry (`TECH`)

`TECH` MUST use the canonical identifier defined in this registry. When adding a technology or technical domain that is not yet listed, a new identifier MAY be added. New identifiers SHOULD be short, natural, widely recognizable, and unambiguous; their length is not fixed.

Where a technology has both a language-independent form and one or more dialect- or product-specific forms, these are registered as separate identifiers (see `SQL` / `TSQL` / `PGSQL` below) rather than folded into one, to make a rule's applicability explicit.

| Identifier | Technology / Domain |
| :--------- | :------------------- |
| `AI`       | Artificial Intelligence / AI Assistants |
| `BASH`     | Bash Shell |
| `BI`       | Business Intelligence — general, tool-independent |
| `DAX`      | Data Analysis Expressions |
| `EXCEL`    | Microsoft Excel |
| `M`        | Power Query M Language |
| `PBI`      | Microsoft Power BI — product-specific |
| `PGSQL`    | PostgreSQL — dialect-specific guidelines |
| `PHP`      | PHP |
| `PY`       | Python |
| `SQL`      | SQL — dialect-agnostic guidelines based on the SQL standard |
| `TSQL`     | Transact-SQL (SQL Server / Azure SQL) — dialect-specific guidelines |
| `VBA`      | Visual Basic for Applications |
| `WORD`     | Microsoft Word |
| `ZSH`      | Zsh Shell |

### Category Registry (`CATEGORY`)

`CATEGORY` MUST use a canonical identifier from this registry whenever an existing category accurately describes the subject of the rule. New categories MAY be added when no existing identifier fits without making the rule misleading or overly broad.

Category identifiers are semantic labels, not fixed-length codes. Their length is not fixed, and natural readability takes precedence over uniform length. Synonymous or near-duplicate categories SHOULD NOT be introduced.

Category identifiers use the **non-plural form** by convention (e.g. `TYPE`, not `TYPES`; `ERROR`, not `ERRORS`). A natural gerund or mass noun MAY be used where it better represents the subject (e.g. `NAMING`, `FORMATTING`, `TESTING`, `DOCUMENTATION`).

| Identifier | Intended Scope |
| :--------- | :--------------- |
| `GENERAL`       | Technology-wide principles, defaults, fallback rules, and references to external standards |
| `NAMING`        | Naming conventions for objects, variables, files, identifiers, and related elements |
| `FORMATTING`    | Layout, whitespace, indentation, line breaks, and other presentation rules |
| `STRUCTURE`     | Organization and composition of code, documents, files, modules, or other artifacts |
| `COMMENT`       | Inline comments, code comments, annotations, and rules governing their use |
| `DOCUMENTATION` | Docstrings, README content, user-facing or developer-facing documentation |
| `TYPE`          | Data types, type declarations, type annotations, conversions, and related rules |
| `ERROR`         | Error handling, exceptions, failure behavior, and recovery |
| `VALIDATION`    | Validation of input, data, assumptions, parameters, or state |
| `SECURITY`      | Security-related practices, restrictions, and safeguards |
| `PERFORMANCE`   | Performance, efficiency, resource usage, and optimization |
| `TESTING`       | Automated or manual testing practices and test organization |
| `CONFIGURATION` | Configuration, settings, environment-specific behavior, and configuration files |
| `DEPENDENCY`    | External libraries, packages, modules, extensions, and dependency management |
| `TABLE`         | Database table design and table-specific rules |
| `VIEW`          | Database view design and view-specific rules |
| `QUERY`         | SQL queries and query construction |
| `TRANSFORM`     | Data transformation steps (e.g. Power Query M steps, ETL pipeline stages) |

When present, `GENERAL` SHOULD be the first category in a technology-specific guideline document. It SHOULD be used only for rules that apply broadly to the technology or define default behavior when no more specific rule applies.

### Examples

* `SQL-TABLE-010`: Naming conventions for database tables
* `TSQL-TABLE-010`: Required columns for system-versioned temporal tables
* `PY-TYPE-010`: Explicit type hinting for public functions
* `M-TRANSFORM-010`: Standard structure for Power Query cleanup steps
* `VBA-ERROR-010`: Explicit error handling implementation

## 3. Guideline Document Structure

Each technology-specific guideline document **MUST** use the following heading hierarchy:

```markdown
# [TECH]: [Technology / Domain Name]

## [CATEGORY]

### [TECH]-[CATEGORY]-[NUMBER]: [Short Rule Title]
```

The level-1 heading identifies the technology or domain, level-2 headings group rules by category, and level-3 headings identify individual rules.

If the `GENERAL` category is present, it **SHOULD** be the first category in the document. The order of all other categories **MAY** be chosen to best fit the technology and the content of the guideline.

## 4. Standard Rule Structure

Every technical rule in a guideline document **MUST** use the following core structure. Optional sections should be included only when they add useful information and should be omitted rather than left empty.

````markdown
### [TECH]-[CATEGORY]-[NUMBER]: [Short Rule Title]

* Requirement: **[MUST | MUST NOT | SHOULD | SHOULD NOT | MAY]**
* Scope: [Optional — include only when the rule does not apply generally]

[Clear and actionable description of the rule.]

#### Rationale
[Optional — explain why the rule exists when the reason is not obvious or is worth preserving.]

#### Good Example
[Optional — include when a compliant example improves understanding.]

```[language]
[Compliant code snippet]
```

#### Bad Example
[Optional — include when a non-compliant example improves understanding.]

```[language]
[Non-compliant code snippet]
```
````

**Markdown Note:** Because the rule template contains fenced code blocks using three backticks, the outer fence used to present the complete Markdown template MUST use four backticks. This prevents the inner code fences from prematurely closing the outer block.

### Why There Is No Status Field

Guideline documents intentionally contain only current rules. A rule's presence in the current version of a guideline document means that it is current and active; its applicability is determined by the rule itself and, when present, its scope. Therefore, rules do not have an explicit `Status` field.

Obsolete rules **SHOULD** be removed rather than retained as `DEPRECATED`. Previous versions remain available through Git history. This is a deliberate design choice intended to keep the repository current, concise, easy to read, and low-maintenance.

## 5. Meta-Guidelines for Writing Guidelines

When adding or modifying guidelines in this repository, follow these core principles:

1. **Clear and Actionable Rules**: Rules **SHOULD** state what to do or avoid in concrete terms. Avoid vague advice such as "write clean code", "use meaningful names", or "follow best practices" unless the document defines what those phrases mean in the relevant context.
2. **One Rule, One Subject**: A rule **SHOULD** address one coherent requirement or a closely related set of requirements. Independent requirements **SHOULD** use separate Rule IDs.
3. **Language**: All guideline files **MUST** be written in English.
4. **International Standards and Units**: Guidelines **SHOULD** prefer established international standards over locale-specific or ad-hoc conventions. Dates and timestamps **SHOULD** use [RFC 3339](https://datatracker.ietf.org/doc/html/rfc3339) representations where applicable; other [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) representations **MAY** be used when they better fit the specific domain or data being represented. Relevant time zones **SHOULD** be stated explicitly. Physical quantities **MUST** use SI units unless another unit is required by an external system, interface, protocol, or domain-specific standard.
5. **Explicit Scope When Needed**: If a rule applies only to a subset of a technology or only under specific conditions, its scope **SHOULD** be stated explicitly. A `Scope` field is optional and **SHOULD NOT** be added when the scope is already obvious.
6. **Rationale When Valuable**: A rationale is optional. It **SHOULD** be included when the reason behind a rule is non-obvious, easy to forget, or important for making future decisions. A rationale **MUST NOT** introduce additional normative requirements that are absent from the rule itself.
7. **Examples When Useful**: Examples are optional. A rule may contain a Good Example, a Bad Example, both, or neither. Examples **SHOULD** be included when they materially improve understanding, especially for complex or non-obvious rules. Examples **MUST NOT** introduce additional normative requirements.
8. **Explicit Exceptions**: Known and intentional exceptions **SHOULD** be stated explicitly when they are important for applying the rule correctly.
9. **Consistency**: Guidelines **SHOULD NOT** contain contradictory rules. If a rule intentionally defines an exception to another rule, that exception **SHOULD** be stated explicitly rather than left for the reader to infer.
10. **Minimalism**: A new rule **SHOULD** be added only when it captures a principle worth remembering, helps make a recurring decision, or prevents a real and recurring problem. Avoid documenting low-value preferences merely for completeness.
