# Python `argparse` --- Detailed Notes

## 1. What is `argparse`?

`argparse` is a Python standard-library module used to build programs
that accept command-line arguments and flags.

Example:

``` bash
python program.py input.txt
```

or:

``` bash
python program.py --copy 5 input.txt
```

This is especially useful for DevOps automation because the same script
can receive different values from Jenkins, GitLab CI, shell scripts,
cron, etc.

------------------------------------------------------------------------

## 2. Basic structure

``` python
import argparse

parser = argparse.ArgumentParser(
    description="A program that processes files"
)

parser.add_argument("file_name")

args = parser.parse_args()

print(args.file_name)
```

Run:

``` bash
python program.py file.txt
```

Output:

``` text
file.txt
```

The normal flow is:

``` text
Create parser
    ↓
Add arguments
    ↓
Parse command-line arguments
    ↓
Use values in the program
```

------------------------------------------------------------------------

## 3. `ArgumentParser()`

Create the parser:

``` python
parser = argparse.ArgumentParser()
```

Add a description:

``` python
parser = argparse.ArgumentParser(
    description="A program that processes files"
)
```

`argparse` automatically provides `-h` and `--help` unless help has been
disabled.

``` bash
python program.py --help
```

------------------------------------------------------------------------

## 4. `add_argument()`

Arguments are defined with:

``` python
parser.add_argument(...)
```

Example:

``` python
parser.add_argument(
    "file_name",
    help="Name of the file to process"
)
```

Then:

``` bash
python program.py file.txt
```

and:

``` python
args.file_name
```

contains:

``` text
file.txt
```

------------------------------------------------------------------------

# 5. Positional arguments

A positional argument does not start with `-` or `--`.

``` python
parser.add_argument("file_name")
```

Run:

``` bash
python program.py file.txt
```

A positional argument is required by default.

If you run:

``` bash
python program.py
```

`argparse` reports that `file_name` is required.

------------------------------------------------------------------------

# 6. Optional arguments / flags

An optional argument normally starts with `-` or `--`.

``` python
parser.add_argument(
    "-c",
    "--copy"
)
```

You can use:

``` bash
python program.py --copy 5
```

or:

``` bash
python program.py -c 5
```

It is good practice to provide a descriptive long name.

------------------------------------------------------------------------

# 7. `parse_args()`

This line reads the command-line input:

``` python
args = parser.parse_args()
```

If the command is:

``` bash
python program.py --copy 5
```

then:

``` python
args.copy
```

contains the supplied value.

------------------------------------------------------------------------

# 8. `help`

Use `help=` to describe an argument:

``` python
parser.add_argument(
    "file_name",
    help="Name of the file to process"
)
```

Then:

``` bash
python program.py -h
```

shows the description automatically.

Good help text is important for CLI tools.

------------------------------------------------------------------------

# 9. `action="store"`

`store` is the default action for a normal option.

``` python
parser.add_argument(
    "-c",
    "--copy",
    action="store"
)
```

You normally don't need to write `action="store"` explicitly.

It expects a value:

``` bash
python program.py --copy 5
```

------------------------------------------------------------------------

# 10. `metavar`

`metavar` controls the name shown for the expected value in help output.

``` python
parser.add_argument(
    "-c",
    "--copy",
    metavar="N",
    help="Make N copies"
)
```

The help message can show:

``` text
-c N, --copy N    Make N copies
```

`metavar` does not change the Python attribute name.

You still use:

``` python
args.copy
```

------------------------------------------------------------------------

# 11. `store_const`

`store_const` stores a predefined constant when the flag is supplied.

``` python
parser.add_argument(
    "-s",
    "--something",
    action="store_const",
    const=15
)
```

Run:

``` bash
python program.py --something
```

Then:

``` python
args.something
```

is:

``` text
15
```

No value is required after the flag.

------------------------------------------------------------------------

# 12. `store_true`

Use `store_true` for boolean flags.

``` python
parser.add_argument(
    "--verbose",
    action="store_true"
)
```

With:

``` bash
python program.py --verbose
```

you get:

``` python
args.verbose == True
```

Without the flag:

``` python
args.verbose == False
```

Common examples:

``` text
--verbose
--debug
--dry-run
--force
```

------------------------------------------------------------------------

# 13. `store_false`

`store_false` provides the opposite boolean behavior.

Example:

``` python
parser.add_argument(
    "--disable-cache",
    action="store_false"
)
```

Use it when the presence of an option should set a value to `False`.

------------------------------------------------------------------------

# 14. `dest`

Normally:

``` python
parser.add_argument("-c", "--copy")
```

creates:

``` python
args.copy
```

You can change the Python attribute name:

``` python
parser.add_argument(
    "-c",
    "--copy",
    dest="number_of_copies"
)
```

Now:

``` python
args.number_of_copies
```

contains the value.

`dest` changes the Python attribute name, not the CLI option.

------------------------------------------------------------------------

# 15. `action="version"`

You can provide a program version:

``` python
parser.add_argument(
    "-v",
    "--version",
    action="version",
    version="program.py 1.0"
)
```

Then:

``` bash
python program.py --version
```

prints:

``` text
program.py 1.0
```

------------------------------------------------------------------------

# 16. `type`

Command-line values are normally read as strings.

``` python
parser.add_argument("--copy")
```

means that:

``` bash
python program.py --copy 5
```

initially gives:

``` python
args.copy == "5"
```

Use `type=int` to convert and validate:

``` python
parser.add_argument(
    "--copy",
    type=int
)
```

Now:

``` python
args.copy
```

is an integer.

If the user enters:

``` bash
python program.py --copy hello
```

`argparse` reports an invalid integer.

Common types:

``` python
type=str
type=int
type=float
```

A callable can also be used for custom conversion.

------------------------------------------------------------------------

# 17. `default`

Use `default=` when an argument should have a value if the user does not
provide it.

``` python
parser.add_argument(
    "--workers",
    type=int,
    default=5
)
```

Then:

``` bash
python program.py
```

gives:

``` python
args.workers == 5
```

while:

``` bash
python program.py --workers 10
```

gives:

``` python
args.workers == 10
```

------------------------------------------------------------------------

# 18. `required=True`

An optional argument can be made mandatory:

``` python
parser.add_argument(
    "--project",
    required=True
)
```

Then the user must provide it.

Use this carefully because options are normally intended to be optional.

------------------------------------------------------------------------

# 19. `choices`

`choices` restricts allowed values.

``` python
parser.add_argument(
    "--environment",
    choices=["dev", "qs", "prod"]
)
```

Valid:

``` bash
python program.py --environment dev
```

Invalid:

``` bash
python program.py --environment test
```

This is very useful in DevOps scripts.

------------------------------------------------------------------------

# 20. `nargs`

`nargs` controls how many values an argument accepts.

### Exactly two

``` python
parser.add_argument(
    "files",
    nargs=2
)
```

Run:

``` bash
python program.py file1.txt file2.txt
```

Result:

``` python
args.files == ["file1.txt", "file2.txt"]
```

### `nargs="?"`

Zero or one value:

``` python
parser.add_argument(
    "file",
    nargs="?",
    default="default.txt"
)
```

### `nargs="*"`

Zero or more values:

``` python
parser.add_argument(
    "files",
    nargs="*"
)
```

### `nargs="+"`

One or more values:

``` python
parser.add_argument(
    "files",
    nargs="+"
)
```

`+` requires at least one value.

------------------------------------------------------------------------

# 21. `action="append"`

`append` allows the same option to be used multiple times:

``` python
parser.add_argument(
    "--repo",
    action="append"
)
```

Run:

``` bash
python program.py \
    --repo repo1 \
    --repo repo2 \
    --repo repo3
```

Result:

``` python
args.repo
```

is:

``` python
["repo1", "repo2", "repo3"]
```

Even one occurrence produces a list.

------------------------------------------------------------------------

# 22. `action="append_const"`

`append_const` appends predefined constants to a list.

``` python
parser.add_argument(
    "--one",
    dest="values",
    action="append_const",
    const=1
)

parser.add_argument(
    "--two",
    dest="values",
    action="append_const",
    const=2
)
```

Using:

``` bash
python program.py --one --two
```

can produce:

``` python
args.values == [1, 2]
```

Multiple options can contribute to the same destination.

------------------------------------------------------------------------

# 23. `action="count"`

`count` counts how many times an option is supplied.

``` python
parser.add_argument(
    "-v",
    "--verbose",
    action="count",
    default=0
)
```

Examples:

``` bash
python program.py
```

gives:

``` text
0
```

``` bash
python program.py -v
```

gives:

``` text
1
```

``` bash
python program.py -v -v -v
```

gives:

``` text
3
```

This is useful for verbosity levels.

------------------------------------------------------------------------

# 24. `action="extend"`

`extend` is useful when each occurrence contributes multiple values to
one flat list.

``` python
parser.add_argument(
    "-n",
    "--number",
    action="extend",
    nargs="+"
)
```

Example:

``` bash
python program.py -n 3 4 -n 5 6
```

Result:

``` python
[3, 4, 5, 6]
```

Compare:

``` text
append  → keeps each occurrence as a separate item
extend  → combines the values into one flat list
```

------------------------------------------------------------------------

# 25. Hiding an argument from help

You can hide an argument using:

``` python
help=argparse.SUPPRESS
```

Example:

``` python
parser.add_argument(
    "--internal",
    help=argparse.SUPPRESS
)
```

The argument still works, but it normally won't appear in the generated
help.

Use this carefully because hidden options are harder for users to
discover.

------------------------------------------------------------------------

# 26. Automatic help and errors

One major benefit of `argparse` is automatic validation and help.

``` bash
python program.py --help
```

shows usage, descriptions, positional arguments, and options.

`argparse` also reports errors for things such as:

-   missing required arguments
-   invalid integer values
-   invalid `choices`
-   incorrect numbers of values
-   unknown options

This means you don't need to manually implement all basic CLI
validation.

------------------------------------------------------------------------

# 27. Your Bitbucket Automation Script

Your Bitbucket script uses the same concepts:

``` python
import argparse

my_parser = argparse.ArgumentParser()

my_parser.add_argument(
    "-b",
    "--branches",
    action="store",
    type=str,
    required=True
)

my_parser.add_argument(
    "-m",
    "--metaDataFile",
    action="store",
    type=str,
    required=True
)

my_parser.add_argument(
    "-u",
    "--user",
    action="store",
    type=str,
    required=True
)

my_parser.add_argument(
    "-s",
    "--secret",
    action="store",
    type=str,
    required=True
)

args = my_parser.parse_args()
```

A command can conceptually look like:

``` bash
python script.py \
    --branches "develop,ci_compliance" \
    --metaDataFile metadata.json \
    --user myuser \
    --secret mytoken \
    --project MYPROJECT \
    --excludeRepos "repo1*,repo2*" \
    --workersLimit 10 \
    --outputFile output.json
```

The values are then available as:

``` python
args.branches
args.metaDataFile
args.user
args.secret
args.project
args.excludeRepos
args.workersLimit
args.outputFile
```

Understanding `argparse` explains an important part of that DevOps
automation script.

------------------------------------------------------------------------

# 28. Recommended Learning Order

Learn these in this order:

``` text
1. ArgumentParser()
        ↓
2. add_argument()
        ↓
3. parse_args()
        ↓
4. Positional arguments
        ↓
5. Optional arguments
        ↓
6. Short and long options
        ↓
7. help
        ↓
8. type
        ↓
9. default
        ↓
10. required
        ↓
11. store_true / store_false
        ↓
12. choices
        ↓
13. nargs
        ↓
14. dest
        ↓
15. append
        ↓
16. count
        ↓
17. extend
        ↓
18. store_const / append_const
```

------------------------------------------------------------------------

# 29. Quick Cheat Sheet

  ---------------------------------------------------------------------------------
  Feature                 Purpose                 Example
  ----------------------- ----------------------- ---------------------------------
  `ArgumentParser()`      Create parser           `argparse.ArgumentParser()`

  `add_argument()`        Add CLI argument        `parser.add_argument("--name")`

  `parse_args()`          Read CLI input          `args = parser.parse_args()`

  Positional              Required value          `"filename"`

  `-x`                    Short option            `-v`

  `--name`                Long option             `--version`

  `help`                  Describe argument       `help="..."`

  `type`                  Convert/validate        `type=int`

  `default`               Default value           `default=10`

  `required`              Make option mandatory   `required=True`

  `store`                 Store supplied value    `action="store"`

  `store_true`            Boolean flag            `--verbose`

  `store_false`           Reverse boolean flag    `--disable-cache`

  `store_const`           Store fixed value       `const=15`

  `choices`               Restrict values         `choices=["dev","prod"]`

  `nargs=2`               Exactly 2 values        `--files a b`

  `nargs="?"`             Zero or one             optional value

  `nargs="*"`             Zero or more            many values

  `nargs="+"`             One or more             many required values

  `dest`                  Change Python attribute `dest="output"`

  `append`                Repeated option → list  `-f a -f b`

  `append_const`          Append constants        repeated flags

  `count`                 Count repeated option   `-vvv`

  `extend`                Extend one flat list    `-n 1 2 -n 3 4`

  `version`               Show program version    `--version`
  ---------------------------------------------------------------------------------

------------------------------------------------------------------------

# 30. Practice Questions

## Practice 1 --- Name

Create:

``` bash
python program.py Ashraf
```

Expected:

``` text
Hello Ashraf
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("name")

args = parser.parse_args()

print(f"Hello {args.name}")
```

------------------------------------------------------------------------

## Practice 2 --- Integer

Accept:

``` bash
python program.py --age 30
```

Expected:

``` text
You are 30 years old.
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--age",
    type=int,
    required=True
)

args = parser.parse_args()

print(f"You are {args.age} years old.")
```

------------------------------------------------------------------------

## Practice 3 --- DevOps Environment

Allow only:

``` text
dev
qs
prod
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "-e",
    "--environment",
    choices=["dev", "qs", "prod"],
    required=True
)

args = parser.parse_args()

print(f"Deploying to {args.environment}")
```

Test:

``` bash
python deploy.py --environment test
```

It should fail.

------------------------------------------------------------------------

## Practice 4 --- Boolean Flag

Create:

``` bash
python deploy.py --dry-run
```

Expected:

``` text
Dry run enabled
```

Without the flag:

``` text
Real deployment
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--dry-run",
    action="store_true"
)

args = parser.parse_args()

if args.dry_run:
    print("Dry run enabled")
else:
    print("Real deployment")
```

------------------------------------------------------------------------

## Practice 5 --- Default Workers

Default workers should be `5`.

``` bash
python program.py
```

Output:

``` text
Workers: 5
```

But:

``` bash
python program.py --workers 10
```

should output:

``` text
Workers: 10
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--workers",
    type=int,
    default=5
)

args = parser.parse_args()

print(f"Workers: {args.workers}")
```

------------------------------------------------------------------------

## Practice 6 --- Multiple Values

Accept:

``` bash
python program.py repo1 repo2 repo3
```

Print each repository.

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "repositories",
    nargs="+"
)

args = parser.parse_args()

for repo in args.repositories:
    print(repo)
```

------------------------------------------------------------------------

## Practice 7 --- Repeated Option

Allow:

``` bash
python program.py \
    --repo repo1 \
    --repo repo2 \
    --repo repo3
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--repo",
    action="append"
)

args = parser.parse_args()

print(args.repo)
```

Output:

``` text
['repo1', 'repo2', 'repo3']
```

------------------------------------------------------------------------

## Practice 8 --- Verbosity

Make:

``` bash
python program.py
```

return:

``` text
0
```

and:

``` bash
python program.py -v -v -v
```

return:

``` text
3
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "-v",
    "--verbose",
    action="count",
    default=0
)

args = parser.parse_args()

print(args.verbose)
```

------------------------------------------------------------------------

## Practice 9 --- Integer Validation

Accept:

``` bash
python program.py --workers 10
```

but reject:

``` bash
python program.py --workers hello
```

### Answer

``` python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--workers",
    type=int,
    required=True
)

args = parser.parse_args()

print(f"Workers: {args.workers}")
```

------------------------------------------------------------------------

# 31. Mini Project --- DevOps Deployment CLI

Create:

``` text
deploy.py
```

It must accept:

``` text
-e / --environment
-v / --version
--dry-run
--workers
```

Requirements:

-   `environment` must be `dev`, `qs`, or `prod`
-   `version` is required
-   `dry-run` is optional
-   `workers` defaults to `5`
-   `workers` must be an integer

Run:

``` bash
python deploy.py \
    --environment dev \
    --version 1.2.3 \
    --workers 10 \
    --dry-run
```

Expected:

``` text
Environment: dev
Version: 1.2.3
Workers: 10
Dry run: True
```

### Solution

``` python
import argparse

parser = argparse.ArgumentParser(
    description="Deploy an application"
)

parser.add_argument(
    "-e",
    "--environment",
    choices=["dev", "qs", "prod"],
    required=True,
    help="Deployment environment"
)

parser.add_argument(
    "-v",
    "--version",
    required=True,
    help="Application version"
)

parser.add_argument(
    "--workers",
    type=int,
    default=5,
    help="Number of workers"
)

parser.add_argument(
    "--dry-run",
    action="store_true",
    help="Run without performing the actual deployment"
)

args = parser.parse_args()

print(f"Environment: {args.environment}")
print(f"Version: {args.version}")
print(f"Workers: {args.workers}")
print(f"Dry run: {args.dry_run}")
```

------------------------------------------------------------------------

# 32. Final Mental Model

``` text
Terminal
   │
   │ python deploy.py --environment dev --version 1.2.3
   ▼
argparse
   │
   ├── Parse arguments
   ├── Validate required values
   ├── Validate types
   ├── Validate choices
   ├── Apply defaults
   └── Handle flags
   │
   ▼
args object
   │
   ├── args.environment
   ├── args.version
   ├── args.workers
   └── args.dry_run
   │
   ▼
Your Python program
```

## Key things to remember

``` text
ArgumentParser()
    → creates the parser

add_argument()
    → defines what the CLI accepts

parse_args()
    → reads the command-line input

Positional argument
    → normally required

--option
    → optional argument/flag

type=int
    → converts and validates input as an integer

default=
    → value used when the argument isn't supplied

required=True
    → makes an optional option mandatory

choices=
    → restricts allowed values

store_true
    → boolean flag

nargs
    → controls number of values

append
    → repeated option creates a list

count
    → counts repeated options

dest
    → changes the Python attribute name
```

## Next learning step for DevOps Python

After `argparse`, connect it with:

``` text
argparse
   ↓
JSON
   ↓
Functions
   ↓
Lists & dictionaries
   ↓
Exception handling
   ↓
File handling
   ↓
requests / REST API
   ↓
HTTP authentication
   ↓
API automation
   ↓
Concurrency / Futures
```
