# Technical Guidelines & Standards

This repository serves as the Single Source of Truth (SSOT) for technical standards, best practices, and project conventions across software engineering, data management, Business Intelligence (BI), and system configuration. It is designed to be clear, structured, and directly actionable for both human engineers (including me in the first place) and Large Language Models (LLMs / AI assistants).

A full list of available standards is maintained in the [TOC.md](TOC.md).

## 1. Requirement Levels (RFC 2119)

All guidelines use standard severity keywords as defined in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) and [RFC 8174](https://datatracker.ietf.org/doc/html/rfc8174). When written in ALL CAPS, they carry the following interpretations:

* **MUST / MUST NOT**: Absolute requirement. Code or configuration that violates a MUST rule is considered broken, non-compliant, or unsafe.
* **SHOULD / SHOULD NOT**: Strong recommendation. Valid exceptions may exist in specific contexts, but they must be explicitly justified during code review.
* **MAY**: Fully optional. Represents a preferred convention when multiple valid choices exist.

## 2. Rule Identification Protocol

Every rule **MUST** have a permanent, unique identifier using the following format:

`[TECH]-[CATEGORY]-[NUMBER]`

### Format Specifications:

* **TECH**: The shortest, widely recognized uppercase abbreviation for the technology (e.g., `SQL`, `PY`, `M`, `VBA`, `DAX`, `ZSH`). It **MUST** be concise, natural, and unambiguous. Its length is not fixed in advance.
* **CATEGORY**: An uppercase word or natural domain identifier representing the subject area (e.g., `TABLE`, `VIEW`, `QUERY`, `FORMATTING`, `SECURITY`). It **MUST** be as short, natural, and clear as possible. Its length is not fixed in advance.
* **NUMBER**: A 3-digit sequential number. Numbers **SHOULD** increment by 10 (e.g., `010`, `020`, `030`) to allow inserting intermediate rules if needed.

### Key Principles:

1. **No Arbitrary Length Caps**: Neither `TECH` nor `CATEGORY` has a fixed character limit. Natural readability, clarity, and widespread adoption always supersede rigid length restrictions.
2. **Immutability**: Once assigned and published, a Rule ID **MUST NOT** be modified or re-used, even if the underlying rule evolves, changes direction, or becomes obsolete. If a rule is retired, change its status to `[DEPRECATED]`.

### Technology Registry (`TECH`):

To maintain repository-wide consistency, use the following standardized identifiers:

| Identifier | Technology / Domain |
| :--- | :--- |
| `SQL` | Relational Databases & SQL Dialects |
| `PY` | Python |
| `M` | Power Query M Language |
| `DAX` | Data Analysis Expressions |
| `PBI` | Power BI (Architecture & General) |
| `VBA` | Visual Basic for Applications |
| `ZSH` | Zsh Shell Scripts |
| `BASH` | Bash / Generic Shell Scripts |

### Examples:
* `SQL-TABLE-010`: Naming conventions for database tables
* `PY-TYPES-010`: Explicit type hinting for public functions
* `M-TRANSFORM-010`: Standard structure for Power Query cleanup steps
* `VBA-ERROR-010`: Explicit error handling implementation

## 3. Standard Rule Structure

Every rule in a guideline document **MUST** follow this structural template to ensure parsing consistency for both humans and AI:

````markdown
### [TECH]-[CATEGORY]-[NUMBER]: [Short Rule Title]

* Severity: **[MUST | SHOULD | MAY]**
* Status: **[ACTIVE | DEPRECATED | EXPERIMENTAL]**

[Clear, concise description of the rule in 1-3 sentences.]

#### Good Example
```[language]
[Compliant code snippet]
```

#### Bad Example
```[language]
[Non-compliant code snippet]
```
````

## 4. Meta-Guidelines for Writing Guidelines

When adding or modifying guidelines in this repository, follow these core principles:

1. **AI-Friendly Precision:** Avoid conversational filler, rhetorical questions, or vague guidance (e.g., instead of "Write clean code", state "Functions MUST NOT exceed 30 lines").
2. **Language:** All files **MUST** be written in English.
3. **Explicit Data & Types:** Specify ISO formats (`YYYY-MM-DD`), SI units, and standard timezone awareness (`UTC` / `TIMESTAMP WITH TIME ZONE`).
4. **Concrete Examples:** Always provide paired `Good Example` and `Bad Example` snippets for complex or non-obvious rules.
5. **Immutability of IDs:** Never re-use or rename an existing Rule ID. If a rule becomes obsolete, mark it as `[DEPRECATED]` instead of deleting its ID.
