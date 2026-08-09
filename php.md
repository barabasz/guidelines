# PHP: Hypertext Preprocessor

**Status:** working draft — early stub, expanded incrementally as needs arise.

## Table of Contents

- [GENERAL](#general)
- [NAMING](#naming)
- [FORMATTING](#formatting)

## GENERAL

### PHP-GENERAL-010: Default Coding Standard

* Requirement: **SHOULD**

PHP code SHOULD follow the current PHP-FIG Coding Style specification unless a more specific rule in these guidelines states otherwise.

## NAMING

### PHP-NAMING-010: Identifier casing conventions

* Requirement: **MUST**

Each kind of identifier MUST use the casing convention listed below.

| Identifier | Convention | Example |
|---|---|---|
| Class | PascalCase | `OrderProcessor` |
| Interface | PascalCase, suffixed `Interface` | `LoggerInterface` |
| Trait | PascalCase, suffixed `Trait` | `LoggableTrait` |
| Enum | PascalCase | `OrderStatus` |
| Namespace | PascalCase segments | `App\Domain\Order` |
| Method | camelCase | `calculateTotal()` |
| Function | snake_case | `calculate_total()` |
| Variable | snake_case | `$order_total` |
| Constant | SCREAMING_SNAKE_CASE | `MAX_RETRY_COUNT` |

#### Rationale
Class, method, and constant casing follow the mandatory conventions of PSR-1. Function and variable casing are not mandated by PSR-1, but `snake_case` matches the naming style of PHP's own standard library (`str_replace`, `array_map`) and is the dominant convention across the PHP ecosystem. Interface, trait, and enum casing extend the class convention by analogy, since PHP treats them as sibling declaration types.

### PHP-NAMING-020: File name matches class name

* Requirement: **MUST**

A file that declares exactly one class, interface, trait, or enum MUST be named after that declaration, with a `.php` extension (e.g. `OrderProcessor.php` for `class OrderProcessor`).

#### Rationale
This is required for PSR-4 autoloading to resolve the class from its namespace and file path without additional configuration.

## FORMATTING

### PHP-FORMATTING-010: Indentation

* Requirement: **MUST**

PHP code MUST use an indent of 4 spaces for each indentation level. Tabs MUST NOT be used for indenting.

#### Rationale
This matches PSR-12 (and PSR-2 before it) exactly, which is enforced by default across the PHP ecosystem's tooling — Composer packages, PHP-CS-Fixer, PHP_CodeSniffer, and IDE defaults. Tabs-for-indentation has a genuine, recognized counter-argument (each reader's editor can render tab width to their own preference, which spaces cannot do) — PHP-FIG itself debated this during PSR-12's drafting and deliberately left it unchanged, treating it as out of scope rather than settled on the merits. This rule follows PSR-12 anyway for practical reasons specific to this project: PHP is a secondary technology here, and deviating from the ecosystem default creates friction with vendor code, formatters, and AI-assisted tooling that all default to PSR-12.

This is a deliberate choice, not an oversight — see PHP-GENERAL-010.
