# Technical Guidelines & Standards

This repository serves as the Single Source of Truth (SSOT) for technical standards, best practices, and project conventions across software engineering, data management, Business Intelligence (BI), and system configuration. It is designed to be clear, concise, and directly actionable for both human engineers (including me in the first place) and Large Language Models (LLMs / AI assistants).

A full list of available standards is maintained in the [TOC.md](TOC.md).

**Disclaimer:** These guidelines are a practical, evolving reference created primarily for my own use, as well as for other interested people and AI assistants. They are not an industry standard, formal specification, or institutional policy, and no long-term semantic stability of individual rules or Rule IDs is guaranteed. The current version of the repository defines the current guidance; Git history preserves how that guidance has evolved over time.

This README defines repository-level conventions for writing and maintaining guideline documents. It is not itself a technology-specific guideline, so the meta-guidelines in this file do not use Rule IDs.

## 1. Requirement Levels (BCP 14)

All guidelines use the requirement keywords defined by BCP 14 ([RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) and [RFC 8174](https://datatracker.ietf.org/doc/html/rfc8174)). These words have their normative meaning only when written in ALL CAPS:

* **MUST / MUST NOT**: Absolute requirement or prohibition. A violation makes the implementation non-compliant with the applicable guidelines.
* **SHOULD / SHOULD NOT**: Strong recommendation. A deviation is permitted only when there is a specific, well-understood reason to do so.
* **MAY**: Fully optional. The rule permits, but does not require, the described behavior.

## 2. Rule Identification Protocol

Every technical rule in a guideline document **MUST** have a stable, unique identifier using the following format:

`[TECH]-[CATEGORY]-[NUMBER]`

### Format Specifications

* **TECH**: The canonical uppercase technology identifier defined in the Technology Registry below. When adding a new technology, choose a short, natural, widely recognized, and unambiguous identifier. Its length is not fixed in advance.
* **CATEGORY**: An uppercase word or natural domain identifier representing the subject area (e.g., `TABLE`, `VIEW`, `QUERY`, `FORMATTING`, `SECURITY`). It **SHOULD** be short, natural, and clear. Its length is not fixed in advance. Existing category identifiers **SHOULD** be reused when they accurately represent the same subject; synonymous categories **SHOULD NOT** be introduced without a specific reason.
* **NUMBER**: A 3-digit number unique within each `TECH-CATEGORY` combination. New rules **SHOULD** normally use increments of 10 (`010`, `020`, `030`, ...). Intermediate numbers **MAY** be used when inserting related rules between existing ones.

### Key Principles

1. **No Arbitrary Length Caps**: Neither `TECH` nor `CATEGORY` has a fixed character limit. Natural readability, clarity, and widespread recognition take precedence over rigid length restrictions.
2. **Stable Identity**: Rule IDs identify rules, not document positions. An existing Rule ID **SHOULD** remain associated with the same general subject and **SHOULD NOT** be reused for an unrelated rule.
3. **Removed IDs**: A Rule ID that has been removed **SHOULD NOT** be reused for a different rule. Gaps in numbering have no semantic meaning and do not indicate missing rules.
4. **Independent Numbering**: Numbering is independent for each `TECH-CATEGORY` combination. For example, `SQL-VIEW-010`, `SQL-TABLE-010`, and `PY-TYPE-010` may all exist at the same time.

### Technology Registry (`TECH`)

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

### Examples

* `SQL-TABLE-010`: Naming conventions for database tables
* `PY-TYPE-010`: Explicit type hinting for public functions
* `M-TRANSFORM-010`: Standard structure for Power Query cleanup steps
* `VBA-ERROR-010`: Explicit error handling implementation

## 3. Standard Rule Structure

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

### Why There Is No Status Field

Guideline documents intentionally contain only current rules. A rule's presence in the current version of a guideline document means that it is current and active; its applicability is determined by the rule itself and, when present, its scope. Therefore, rules do not have an explicit `Status` field.

Obsolete rules **SHOULD** be removed rather than retained as `DEPRECATED`. Previous versions remain available through Git history. This is a deliberate design choice intended to keep the repository current, concise, easy to read, and low-maintenance.

## 4. Meta-Guidelines for Writing Guidelines

When adding or modifying guidelines in this repository, follow these core principles:

1. **Clear and Actionable Rules**: Rules **SHOULD** state what to do or avoid in concrete terms. Avoid vague advice such as "write clean code", "use meaningful names", or "follow best practices" unless the document defines what those phrases mean in the relevant context.
2. **One Rule, One Subject**: A rule **SHOULD** address one coherent requirement or a closely related set of requirements. Independent requirements **SHOULD** use separate Rule IDs.
3. **Language**: All guideline files **MUST** be written in English.
4. **International Standards and Units**: Guidelines **SHOULD** prefer established international standards over locale-specific or ad-hoc conventions. Dates and times **SHOULD** use ISO standards where applicable, and relevant time zones **SHOULD** be stated explicitly. Physical quantities **MUST** use SI units unless another unit is required by an external system, interface, protocol, or domain-specific standard.
5. **Explicit Scope When Needed**: If a rule applies only to a subset of a technology or only under specific conditions, its scope **SHOULD** be stated explicitly. A `Scope` field is optional and **SHOULD NOT** be added when the scope is already obvious.
6. **Rationale When Valuable**: A rationale is optional. It **SHOULD** be included when the reason behind a rule is non-obvious, easy to forget, or important for making future decisions. A rationale **MUST NOT** introduce additional normative requirements that are absent from the rule itself.
7. **Examples When Useful**: Examples are optional. A rule may contain a Good Example, a Bad Example, both, or neither. Examples **SHOULD** be included when they materially improve understanding, especially for complex or non-obvious rules. Examples **MUST NOT** introduce additional normative requirements.
8. **Explicit Exceptions**: Known and intentional exceptions **SHOULD** be stated explicitly when they are important for applying the rule correctly.
9. **Consistency**: Guidelines **SHOULD NOT** contain contradictory rules. If a rule intentionally defines an exception to another rule, that exception **SHOULD** be stated explicitly rather than left for the reader to infer.
10. **Minimalism**: A new rule **SHOULD** be added only when it captures a principle worth remembering, helps make a recurring decision, or prevents a real and recurring problem. Avoid documenting low-value preferences merely for completeness.
