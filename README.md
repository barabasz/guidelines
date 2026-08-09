# Technical Guidelines & Standards

This repository serves as the Single Source of Truth (SSOT) for technical standards, best practices, and project conventions across software engineering, data management, Business Intelligence (BI), and system configuration. It is designed to be clear, structured, and directly actionable for both human engineers (including me in first place) and Large Language Models (LLMs / AI assistants).

## 1. Requirement Levels (RFC 2119)

All guidelines use standard severity keywords as defined in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) and [RFC 8174](https://datatracker.ietf.org/doc/html/rfc8174). When written in ALL CAPS, they carry the following interpretations:

* **MUST / MUST NOT**: Absolute requirement. Code that violates a MUST rule is considered broken or unsafe.
* **SHOULD / SHOULD NOT**: Strong recommendation. Valid exceptions may exist in specific contexts, but they must be explicitly justified during code review.
* **MAY**: Fully optional. Represents a preferred convention when multiple valid choices exist.

## 2. Rule Identification Protocol

Every rule **MUST** have a permanent, unique identifier using the following format:

`[TECH]-[CATEGORY]-[NUMBER]`

### Format Specifications:
* **TECH**: 3-letter uppercase identifier (e.g., `SQL`, `PYT`, `ZSH`, `VBA`).
* **CATEGORY**: 3-letter uppercase category code (e.g., `TAB` for Tables, `VIW` for Views, `FMT` for Formatting).
* **NUMBER**: 3-digit number. Numbers **SHOULD** increment by 10 (e.g., `010`, `020`, `030`) to leave room for future intermediate rules.

### Immutability Constraint:
Once a Rule ID is published and referenced, it **MUST NOT** be changed or re-used, even if the rule's internal requirement changes or becomes obsolete. If a rule is retired, mark its status as `[DEPRECATED]`.

### Examples:
* `SQL-TAB-010`
* `SQL-VIW-020`
* `PYT-FMT-010`
* `VBA-ERR-030`

## 3. Standard Rule Structure

Every rule in a guideline document **SHOULD** follow this structural template to ensure parsing consistency for both humans and AI:

````markdown
## 3. Standard Rule Structure

Every rule in a guideline document **MUST** follow this structural template to ensure parsing consistency for both humans and AI:

### [TECH]-[CAT]-[NNN]: [Short Rule Title]

* Severity: **[MUST | SHOULD | MAY]**
* Status: **[ACTIVE | DEPRECATED | EXPERIMENTAL]**

[Clear, concise description of the rule in 1-3 sentences.]

#### Good Example
```[language]
[Compliant code snippet]
```

#### Bad Example
```[language]
[Compliant code snippet]
```
````

## 4. Meta-Guidelines for Writing Guidelines

When adding or modifying guidelines in this repository, follow these core principles:

1. **AI-Friendly Precision:** Avoid conversational filler, rhetorical questions, or vague guidance (e.g., instead of "Write clean code", state "Functions MUST NOT exceed 30 lines").
2. **Language:** All files **MUST** be written in English.
3. **Explicit Data & Types:** Specify ISO formats (`YYYY-MM-DD`), SI units, and standard timezone awareness (`UTC` / `TIMESTAMP WITH TIME ZONE`).
4. **Concrete Examples:** Always provide paired `Good Example` and `Bad Example` snippets for complex or non-obvious rules.
5. **Immutability of IDs:** Never re-use or rename an existing Rule ID. If a rule becomes obsolete, mark it as `[DEPRECATED]` instead of deleting its ID.
