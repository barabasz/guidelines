# PY: Python

## Table of Contents

- [GENERAL](#general)
- [NAMING](#naming)
- [FORMATTING](#formatting)

## GENERAL

### PY-GENERAL-010: Default Coding Standard

* Requirement: **SHOULD**

Python code SHOULD follow [PEP 8](https://peps.python.org/pep-0008/), the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html), and general Python best current practices, unless a more specific rule in these guidelines states otherwise.

#### Rationale
PEP 8 codifies much of the philosophy set out in [the Zen of Python](https://peps.python.org/pep-0020/) (PEP 20) — readability, explicitness, and one obvious way to do a thing — into concrete, checkable conventions.

## NAMING

### PY-NAMING-010: Identifier casing conventions

* Requirement: **MUST**

Each kind of identifier MUST use the casing convention listed below.

| Identifier | Convention | Example |
|---|---|---|
| Class | PascalCase | `class UserProfile:` |
| Exception | PascalCase, suffixed `Error` when it represents an error condition | `class ValidationError(Exception):` |
| Function / Method | snake_case | `def calculate_total():` |
| Variable (including parameters and locals) | snake_case | `user_age = 25` |
| Constant | SCREAMING_SNAKE_CASE | `TIME_ZONE = "Europe/Warsaw"` |
| Module | short, lowercase; underscores allowed for readability | `data_loader.py` |
| Package | short, lowercase; underscores discouraged | `myapp` |

#### Rationale
This follows PEP 8's own "Naming Conventions" section directly. Exceptions get the class convention because they are classes in Python — the `Error` suffix is PEP 8's own recommendation, applied when the exception represents an actual error condition.

## FORMATTING

### PY-FORMATTING-010: String quote character

* Requirement: **MUST**

Double-quoted strings (`"..."`) MUST be used by default. Single quotes (`'...'`) MAY be used instead specifically to avoid backslash-escaping a double quote that appears inside the string.

#### Rationale
PEP 8 treats single and double quotes as fully equivalent and takes no position on which to prefer — it only asks that a project pick one and apply it consistently. This project's default is double quotes; switching to single quotes when the string itself contains a `"` avoids unnecessary escaping and improves readability.

#### Good Example
```python
s: str = "Hello world!"
greeting = 'She said "hello" to me'   # avoids escaping the double quote
```

#### Bad Example
```python
s: str = 'Hello world!'                 # inconsistent default
greeting = "She said \"hello\" to me"   # unnecessary escaping - use single quotes here
```
