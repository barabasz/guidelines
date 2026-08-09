# ZSH: Zsh Shell

**Goal:** idiomatic zsh code, without bash/POSIX compatibility baggage — for both human- and AI-written scripts.

**Status:** adapted from the `zconfig` project's zsh coding guidelines into this repository's rule format.

## Table of Contents

- [GENERAL](#general)
- [NAMING](#naming)
- [COMMENT](#comment)
- [IDIOM](#idiom)
- [ERROR](#error)
- [STRUCTURE](#structure)

## GENERAL

### ZSH-GENERAL-010: Write for zsh exclusively

* Requirement: **MUST**

Scripts MUST be written for zsh exclusively, using zsh-native constructs, variables, and builtins — not for bash/POSIX compatibility.

#### Good Example
```zsh
is_file() {
    (( ARGC == 1 )) && [[ -f $1 ]]
}

process_args() {
    (( ARGC < 2 )) && { print -u2 "Usage: $0 arg1 arg2"; return 1 }
    local first=$1
    local last=${argv[-1]}
    # ...
}
```

#### Bad Example
```zsh
is_file() {
    if [ $# -eq 1 ] && [ -f "$1" ]; then
        return 0
    else
        return 1
    fi
}
```

### ZSH-GENERAL-020: Shebang required

* Requirement: **MUST**

Every zsh script MUST begin with a `#!/bin/zsh` shebang line.

### ZSH-GENERAL-030: Function-local variable declarations

* Requirement: **MUST**

Variables used only within a function MUST be declared with `local` (`local -a` for arrays, `local -A` for associative arrays).

#### Good Example
```zsh
my_function() {
    local var="value"
    local -a items=(a b c)
}
```

#### Bad Example
```zsh
my_function() {
    var="value"   # leaks into global scope
}
```

## NAMING

### ZSH-NAMING-010: Never shadow tied special arrays

* Requirement: **MUST NOT**

The names `path`, `fpath`, `cdpath`, `mailpath`, and `manpath` MUST NOT be used for local or ad-hoc variables. Each is a special array tied to an environment variable, and shadowing it silently breaks the corresponding shell feature.

| Variable | Tied to | Effect if shadowed |
|----------|---------|-------------------|
| `path` | `PATH` | Commands not found |
| `fpath` | `FPATH` | Autoload functions fail |
| `cdpath` | `CDPATH` | `cd` behavior breaks |
| `mailpath` | `MAILPATH` | Mail checks fail |
| `manpath` | `MANPATH` | `man` can't find pages |

#### Rationale
This is a subtle bug that can be hard to diagnose — commands work outside the function but fail inside it.

#### Good Example
```zsh
my_function() {
    local file_path="/some/path"   # safe
    local found_path=""            # safe
}
```

#### Bad Example
```zsh
my_function() {
    local path="/some/path"        # PATH is now empty!
    whence -p brew                 # fails - PATH is gone
}
```

## COMMENT

### ZSH-COMMENT-010: Section headers vs regular comments

* Requirement: **MUST**

`##` MUST be used for section headers (a logical grouping level within the script, not Markdown syntax). `#` MUST be used for regular comments.

#### Good Example
```zsh
## Configuration section
# Load user config if present
[[ -f $config_file ]] && source $config_file
```

## IDIOM

### ZSH-IDIOM-010: Native special variables over POSIX equivalents

* Requirement: **MUST**

Zsh-native special variables MUST be used instead of their POSIX/bash equivalents: `ARGC` (not `$#`), `argv` (not `$@` or `$*`), `argv[1]` (not `$1`), `status` (not `$?`).

* Exception: `$1`, `$2`, etc. MAY be used when clearer, e.g. in simple functions with 2–3 arguments. `ARGC` is still always preferred over `$#`.

#### Rationale
Inside `(( ))`, variables don't need a `$` prefix, which makes exit-code checks using `status` particularly clean.

#### Good Example
```zsh
ARGC                   # number of arguments (not $#)
argv                   # array of arguments (not $@ or $*)
argv[1]                # first argument (not $1)
argv[-1]                # last argument
status                 # exit code of last command (not $?)
```
```zsh
command
if (( status != 0 )); then
    print -u2 "Command failed with exit code $status"
fi
```

#### Bad Example
```zsh
$#                     # use ARGC instead
$@, $*                 # use argv instead
$?                     # use status instead
```

### ZSH-IDIOM-020: (( )) for all numeric operations

* Requirement: **MUST**

`(( ))` MUST be used for all numeric operations: comparisons, arithmetic, counters, and set/exists checks.

#### Good Example
```zsh
(( ARGC < 2 ))          # argument count
(( count > 0 ))         # variable comparison
(( exit_code == 0 ))    # exit code check
(( ${#array} >= 5 ))    # array length
(( i++ ))               # increment
(( total += n ))        # arithmetic assignment

(( ${+var} ))           # 1 if var is set, 0 otherwise
(( ${+functions[fn]} )) # check if function exists
(( ${+commands[cmd]} )) # check if command exists
```

### ZSH-IDIOM-030: [[ ]] for string and file tests only

* Requirement: **MUST**

`[[ ]]` MUST be used — never the POSIX `[ ]` test — for string and file tests only: existence, type, and readability checks; string equality, emptiness, and pattern matching. `[[ ]]` MUST NOT be used for numeric comparisons — see ZSH-IDIOM-020.

#### Good Example
```zsh
[[ -f $file ]]           # is regular file
[[ -d $path ]]           # is directory
[[ -e $path ]]           # exists
[[ -r $file ]]           # is readable

[[ -n $var ]]            # string is non-empty
[[ -z $var ]]            # string is empty
[[ $str == pattern* ]]   # pattern matching
[[ $a == $b ]]           # string equality
[[ $OSTYPE == darwin* ]] # OS detection
```

#### Bad Example
```zsh
[ -f "$file" ] && [ -r "$file" ]
```

### ZSH-IDIOM-040: POSIX numeric test operators forbidden

* Requirement: **MUST NOT**

POSIX-style numeric test operators (`-lt`, `-gt`, `-eq`, `-ne`, `-le`, `-ge`) inside `[[ ]]` MUST NOT be used. Use the `(( ))` equivalents from ZSH-IDIOM-020 instead.

| Operation | Correct | Wrong |
|-----------|---------|-------|
| less than | `(( a < b ))` | `[[ $a -lt $b ]]` |
| greater than | `(( a > b ))` | `[[ $a -gt $b ]]` |
| equal (numeric) | `(( a == b ))` | `[[ $a -eq $b ]]` |
| not equal (numeric) | `(( a != b ))` | `[[ $a -ne $b ]]` |
| less or equal | `(( a <= b ))` | `[[ $a -le $b ]]` |
| greater or equal | `(( a >= b ))` | `[[ $a -ge $b ]]` |

### ZSH-IDIOM-050: Parameter expansion flags over external pipes

* Requirement: **SHOULD**

Before piping (`|`) to an external tool such as `cut`, `tr`, `sort`, or `uniq`, a zsh parameter expansion flag (`${(flags)var}`) SHOULD be checked first — it can often do the same job natively.

#### Good Example
```zsh
local str="apple,banana,cherry"
local -a fruits

fruits=( ${(s:,:)str} )        # split by delimiter → (apple banana cherry)
print ${(j:--:)fruits}         # join with delimiter → apple--banana--cherry
print ${(U)str}                # uppercase → APPLE,BANANA,CHERRY

local -a nums=(3 1 2 1 3)
print ${(uo)nums}              # unique + sort ascending → 1 2 3

local file="File With * Spaces"
print -r -- ${(q)file}         # quote special characters → File\ With\ \*\ Spaces
```

### ZSH-IDIOM-060: Path modifiers over dirname/basename/realpath

* Requirement: **MUST**

Path modifiers MUST be used instead of calling the external `dirname`, `basename`, or `realpath`.

#### Good Example
```zsh
local file="./src/main.c"

${file:A}           # absolute path   → /home/user/project/src/main.c
${file:t}           # tail (basename) → main.c
${file:r}           # root (no ext)   → ./src/main
${file:e}           # extension       → c
${file:h}           # head (dirname)  → ./src
${file:t:r}         # combine them    → main
${file:s/src/bin/}  # substitution    → ./bin/main.c

print $argv:A        # absolute paths of all arguments
```

### ZSH-IDIOM-070: Globbing qualifiers over find + grep

* Requirement: **SHOULD**

Globbing qualifiers SHOULD be used to filter files directly in glob patterns, instead of piping `find` through `grep`.

#### Good Example
```zsh
*(/)           # directories only
*(.)           # regular files only
*(^/)          # NOT directories
*(.x)          # executable files
*(Lm+5)        # files > 5MB
*(om[1,3])     # 3 newest files (ordered by mtime)

# BASH: find . -maxdepth 1 -type d -not -name '.*'
# ZSH:
echo *(/)
```

### ZSH-IDIOM-080: Array-based PATH manipulation

* Requirement: **MUST**

`path`, `fpath`, and `cdpath` MUST be manipulated as zsh arrays, never as colon-separated strings.

#### Rationale
`typeset -U path` additionally removes duplicate entries automatically — something a colon-separated string can't do natively. See ZSH-NAMING-010 for the danger of shadowing these array names with a local variable.

#### Good Example
```zsh
path+=(/opt/new/bin)
typeset -U path
```

#### Bad Example
```zsh
export PATH=$PATH:/opt/new/bin
```

### ZSH-IDIOM-090: print over echo

* Requirement: **MUST**

`print` MUST be used instead of `echo`. It is more predictable and offers more options.

#### Good Example
```zsh
print -u2 "Error message"
print -u2 -- "Error: $msg"     # safe with variables starting with -

local input="-v"
print -r -- $input             # works correctly, prints "-v"

print -f "Name: %s, Count: %d\n" "$name" "$count"
```

### ZSH-IDIOM-100: Associative arrays for key-value data

* Requirement: **SHOULD**

Associative arrays (`local -A`) SHOULD be used for key-value data, instead of parallel arrays or delimited-string encoding.

#### Good Example
```zsh
local -A config
config[host]="localhost"
config[port]="8080"

print "Connect to $config[host]:$config[port]"

(( ${+config[user]} )) && print "User is set"

for key val in ${(kv)config}; do
    print "$key → $val"
done
```

### ZSH-IDIOM-110: Short-form loops for single-statement bodies

* Requirement: **MAY**

A short-form loop MAY be used instead of the standard block form when the loop body is a single command.

#### Good Example
```zsh
# Standard
for f in *.txt; do
    print -r -- $f
done

# Short form (single command)
for f (*.txt) print -r -- $f
```

### ZSH-IDIOM-120: REPLY/reply over subshell capture

* Requirement: **SHOULD**

Functions SHOULD return values via `$REPLY` (scalar) or `$reply` (array) instead of a subshell capture (`$(...)`), which is slower.

#### Good Example
```zsh
get_data() {
    REPLY="result"           # scalar
    reply=(apple banana)     # array
}

get_data
print $REPLY      # → result
print $reply[2]   # → banana
```

#### Bad Example
```zsh
get_data() {
    print "result"
}
result=$(get_data)
```

### ZSH-IDIOM-130: mapfile over cat in a subshell

* Requirement: **SHOULD**
* Scope: Reading reasonably-sized files

`zsh/mapfile` SHOULD be used instead of `cat` in a subshell for reading file contents.

#### Good Example
```zsh
zmodload zsh/mapfile

local content=$mapfile[file.txt]
mapfile[log.txt]="New log entry"
```

### ZSH-IDIOM-140: zparseopts over getopt/manual case loops

* Requirement: **SHOULD**

`zparseopts` (from `zsh/zutil`) SHOULD be used instead of external `getopt` or a manual `case`-based argument-parsing loop.

#### Good Example
```zsh
zmodload zsh/zutil
local -A opts

zparseopts -D -A opts v=verbose -verbose=v h=help

(( ${+opts[-v]} )) && print "Verbose mode"
(( ${+opts[-h]} )) && print "Help requested"
```

## ERROR

### ZSH-ERROR-010: Standard exit code convention

* Requirement: **MUST**

Functions and scripts MUST use the following standard exit codes. `1` MUST NOT be reused to signal invalid usage — `2` is reserved for that.

| Code | Meaning | When to use |
|------|---------|-------------|
| 0 | Success / true | Operation completed successfully |
| 1 | General error / false | Operation failed, or predicate returned false |
| 2 | Invalid usage | Wrong arguments, missing required params |
| 127 | Not found | Command, file, or resource not found |

#### Rationale
Distinguishing "operation failed" (`1`) from "function called incorrectly" (`2`) lets callers handle the two cases differently. `127` matches the shell's own behavior when a command isn't found.

#### Good Example
```zsh
some_function "$arg"
case $status in
    0)   print "Success" ;;
    1)   print "Failed" ;;
    2)   print "Invalid usage" ;;
    127) print "Not found" ;;
esac
```

### ZSH-ERROR-020: Predicate return convention

* Requirement: **SHOULD**

Predicate (true/false) functions SHOULD return `0` for true (condition met), `1` for false (condition not met), and `2` for invalid usage — following the codes defined in ZSH-ERROR-010.

#### Good Example
```zsh
str_contains() {
    (( ARGC == 2 )) || return 2    # invalid usage
    [[ "$1" == *"$2"* ]]           # returns 0 (true) or 1 (false)
}
```

#### Bad Example
```zsh
str_contains() {
    (( ARGC == 2 )) || return 1    # wrong: 1 for invalid usage
    [[ "$1" == *"$2"* ]]
}
```

## STRUCTURE

### ZSH-STRUCTURE-010: Keep functions small and focused

* Requirement: **SHOULD**

Functions SHOULD be kept small and focused on a single responsibility.

---

*Adapted from the `zconfig` project's zsh coding guidelines. Project-specific tooling conventions (e.g. the `zfile_track_start`/`zfile_track_end` file-tracking calls, the `is_installed` helper) were intentionally left out as out of scope for a technology-wide guideline — see the accompanying note for how to handle project-specific conventions.*
