# General Coding Guidelines

This repository serves as the Single Source of Truth (SSOT) for programming standards across projects. It is designed to be easily readable by both human software engineers and Large Language Models (LLMs / AI assistants).

---

## 1. Requirement Levels (RFC 2119)

All guidelines use standard severity keywords as defined in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) and [RFC 8174](https://datatracker.ietf.org/doc/html/rfc8174). When written in ALL CAPS, they carry the following interpretations:

* **MUST / MUST NOT**: Absolute requirement. Code that violates a MUST rule is considered broken or unsafe.
* **SHOULD / SHOULD NOT**: Strong recommendation. Valid exceptions may exist in specific contexts, but they must be explicitly justified during code review.
* **MAY**: Fully optional. Represents a preferred convention when multiple valid choices exist.

---

## 2. Rule Identification & Naming Protocol

To avoid issues with inserting new rules between existing ones, **do not use sequential numbers** (e.g., `RULE-01`, `RULE-02`). 

Instead, every rule **MUST** have a unique, descriptive slug identifier using the following format:

`RULE-[TECHNOLOGY]-[CATEGORY]-[DESCRIPTIVE-SLUG]`

### Format Rules:
* All letters **MUST** be UPPERCASE.
* Words **MUST** be separated by hyphens (`-`).
* The identifier **MUST** remain permanent once assigned (do not change existing IDs to maintain Git diff stability and prompt consistency).

### Examples:
* `RULE-SQL-NAMING-SNAKE-CASE`
* `RULE-PYTHON-TYPES-EXPLICIT-RETURN`
* `RULE-VBA-VARIABLES-OPTION-EXPLICIT`

---

## 3. Standard Rule Structure

Every rule in a guideline document **SHOULD** follow this structural template to ensure parsing consistency for both humans and AI:

```markdown
### RULE-[TECH]-[CATEGORY]-[SLUG]: [Short Rule Title]

* Severity: **[MUST | SHOULD | MAY]**
* Category: **[Naming | Security | Formatting | Performance | Architecture]**

[Clear, concise description of the rule in 1-3 sentences.]

#### Good Example
/`/`/`[language]
[Compliant code snippet]
/`/`/`

#### Bad Example
/`/`/`[language]
[Compliant code snippet]
/`/`/`
```

## 4. Meta-Guidelines for Writing Guidelines

When adding or modifying guidelines in this repository, follow these core principles:

1. **AI-Friendly Precision:** Avoid conversational filler, rhetorical questions, or vague guidance (e.g., instead of "Write clean code", state "Functions MUST NOT exceed 30 lines").
2. **Language:** All files **MUST** be written in English.
3. **Explicit Data & Types:** Specify ISO formats (`YYYY-MM-DD`), SI units, and standard timezone awareness (`UTC` / `TIMESTAMP WITH TIME ZONE`).
4. **Concrete Examples:** Always provide paired `Good Example` and `Bad Example` snippets for complex or non-obvious rules.
5. **Immutability of IDs:** Never re-use or rename an existing Rule ID. If a rule becomes obsolete, mark it as `[DEPRECATED]` instead of deleting its ID.
