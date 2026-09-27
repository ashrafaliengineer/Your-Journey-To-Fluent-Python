# Python `argparse` — Basic Command-Line Arguments
### video Link: https://www.youtube.com/watch?v=FbEJN8FsJ9U

## 1. What is `argparse`?

If you have Python scripts that need some input from the user, or where you want to change a value to get a different result, `argparse` is a useful way to handle that input.

`argparse` is a Python standard-library module used to add **positional arguments** and **optional arguments/flags** to programs that are executed from the command line.

Instead of changing variables inside the Python file every time, you can pass the values when running the program.

### Example

Instead of:

```python
team = input("Enter team: ")
```

you can run:

```bash
python workingfile.py arsenal
```

The value `arsenal` is passed to the program as a command-line argument.

---

## 2. Why use command-line arguments?

A simple `input()` approach works:

```python
team = input("Enter team: ")
```

But it requires the program to stop and wait for input.

For scripts that you repeatedly run yourself, or scripts used by other people or automation systems, command-line arguments are usually more convenient.

For example:

```bash
python workingfile.py arsenal
```

Then:

```bash
python workingfile.py chelsea
```

You can run the same script with different values without changing the source code.

This style is also common in Linux command-line programs.

---

# 3. Positional vs Optional Arguments

Command-line arguments are commonly divided into two types.

## Positional argument

A positional argument is normally required and is supplied without `-` or `--`.

Example:

```bash
python workingfile.py arsenal
```

Here:

```text
arsenal
```

is the positional argument.

The program needs it because it cannot search for a team's stadium without knowing which team to search for.

## Optional argument

Optional arguments normally use a short or long flag:

```bash
-c 5
```

or:

```bash
--copy 5
```

The user can choose whether to provide them.

### Simple comparison

```text
Positional argument:
python program.py arsenal

Optional argument:
python program.py --team arsenal
```

---

# 4. Example Without `argparse`

The original example uses `requests`, `json`, and `input()`:

```python
import requests
import json

team = input("Enter team: ")

url = "https://www.thesportsdb.com/api/v1/json/1/searchteams.php?t=" + team

r = requests.get(url)

data = json.loads(r.text)

print(data["teams"][0]["strStadium"])
```

The program waits for:

```text
Enter team:
```

and the user types a team.

For example:

```text
arsenal
```

The API response is then used to print the stadium.

---

# 5. Why Replace `input()`?

With `input()`:

```python
team = input("Enter team: ")
```

you have to interact with the program while it is running.

With `argparse`, you can run:

```bash
python workingfile.py arsenal
```

This is cleaner for scripts that are repeatedly executed from a terminal or automation system.

---

# 6. Import `argparse`

First:

```python
import argparse
```

`argparse` is part of Python's standard library.

---

# 7. Create the Argument Parser

The first major step is creating an `ArgumentParser` object:

```python
parser = argparse.ArgumentParser(
    description="finds the stadiums"
)
```

The parser is responsible for:

- defining the arguments
- reading the command-line input
- validating the arguments
- creating the `args` object
- generating help and error messages

The `description` explains what the program does.

---

# 8. Add the Argument

Next, define the argument:

```python
parser.add_argument(
    "team",
    metavar="team",
    type=str,
    help="enter your team"
)
```

Let's understand each part.

### `"team"`

This is the name of the positional argument.

```python
"team"
```

Later, the value is accessed as:

```python
args.team
```

### `metavar="team"`

`metavar` controls how the argument is displayed in the command-line help/usage information.

For example:

```text
usage: workingfile.py [-h] team
```

### `type=str`

This tells `argparse` that the expected value is a string.

```python
type=str
```

### `help="enter your team"`

This provides a description that appears when the user requests help.

For example:

```bash
python workingfile.py -h
```

---

# 9. Parse the Arguments

After defining the arguments:

```python
args = parser.parse_args()
```

This reads the arguments supplied when the program is executed.

For example:

```bash
python workingfile.py arsenal
```

After parsing:

```python
args.team
```

contains:

```text
arsenal
```

---

# 10. Store the Argument in a Variable

The example then does:

```python
team = args.team
```

Now the rest of the program can use the variable:

```python
team
```

For example:

```python
url = "https://www.thesportsdb.com/api/v1/json/1/searchteams.php?t=" + team
```

---

# 11. Complete Example

The complete `argparse` version is:

```python
import requests
import json
import argparse

parser = argparse.ArgumentParser(
    description="finds the stadiums"
)

parser.add_argument(
    "team",
    metavar="team",
    type=str,
    help="enter your team"
)

args = parser.parse_args()

team = args.team

url = "https://www.thesportsdb.com/api/v1/json/1/searchteams.php?t=" + team

r = requests.get(url)

data = json.loads(r.text)

print(data["teams"][0]["strStadium"])
```

Run it like:

```bash
python workingfile.py arsenal
```

The value:

```text
arsenal
```

flows through the program like this:

```text
Command line
     │
     │ arsenal
     ▼
argparse
     │
     ▼
args.team
     │
     ▼
team
     │
     ▼
API URL
     │
     ▼
requests.get()
     │
     ▼
JSON response
     │
     ▼
stadium
```

---

# 12. What Happens If the Argument Is Missing?

Because `team` is a positional argument:

```python
parser.add_argument("team")
```

it is required by default.

If you run:

```bash
python workingfile.py
```

`argparse` reports an error saying that the `team` argument is required.

The usage information also shows:

```text
usage: workingfile.py [-h] team
```

This is useful because the program tells the user exactly what is missing.

---

# 13. Command-Line Help

`argparse` automatically provides help.

Run:

```bash
python workingfile.py -h
```

or:

```bash
python workingfile.py --help
```

The help output includes the program description and the argument information.

Because we added:

```python
help="enter your team"
```

the user gets a useful explanation of what should be supplied.

---

# 14. Screenshot — Original `input()` Version

The first screenshot shows the simple `input()` approach.

![Python script using input() and API request](./images/argparse_basic_input_example.png)

The important part is:

```python
team = input("enter team: ")
```

The program waits for the user to type the team.

---

# 15. Screenshot — `argparse` Version

The second screenshot shows the same program converted to `argparse`.

![Python script using argparse](./images/argparse_command_line_example.png)

The important lines are:

```python
parser = argparse.ArgumentParser(
    description="finds the stadiums"
)

parser.add_argument(
    "team",
    metavar="team",
    type=str,
    help="enter your team"
)

args = parser.parse_args()

team = args.team
```

This replaces the interactive `input()` approach.

---

# 16. Running the Program

Suppose the file is:

```text
workingfile.py
```

Run:

```bash
python workingfile.py arsenal
```

Or on systems where Python 3 is explicitly invoked:

```bash
python3 workingfile.py arsenal
```

The command-line structure is:

```text
python
   ↓
workingfile.py
   ↓
arsenal
```

Here:

```text
workingfile.py → Python program
arsenal        → positional argument
```

---

# 17. Running It With Another Team

You can reuse the same script:

```bash
python workingfile.py arsenal
```

Then:

```bash
python workingfile.py chelsea
```

Then:

```bash
python workingfile.py liverpool
```

The Python source code does not need to change.

Only the command-line argument changes.

---

# 18. Why This Is Useful in DevOps

This same concept is heavily used in DevOps scripts.

For example:

```bash
python deploy.py dev
```

or:

```bash
python deploy.py prod
```

The Python program can use the argument to determine which environment to work with.

A more advanced command could be:

```bash
python deploy.py \
    --environment dev \
    --version 1.2.3
```

This allows CI/CD systems to pass values into Python scripts.

For example:

```text
Jenkins
   ↓
Python script
   ↓
argparse
   ↓
environment / version / other parameters
   ↓
automation
```

---

# 19. Positional Argument in the Example

The example has:

```python
parser.add_argument("team")
```

Because there is no `-` or `--`, it is a positional argument.

Therefore:

```bash
python workingfile.py arsenal
```

is valid.

But:

```bash
python workingfile.py
```

is not valid because `team` is required.

---

# 20. How Optional Arguments Are Different

The video briefly mentions that optional arguments use dashes.

For example:

```python
parser.add_argument(
    "-t",
    "--team",
    type=str,
    help="enter your team"
)
```

Now the user could run:

```bash
python workingfile.py --team arsenal
```

or:

```bash
python workingfile.py -t arsenal
```

Unlike the positional version, this is an optional-style argument.

---

# 21. Important Syntax to Remember

Basic parser:

```python
parser = argparse.ArgumentParser(
    description="Program description"
)
```

Add an argument:

```python
parser.add_argument(
    "argument_name",
    metavar="argument_name",
    type=str,
    help="Description"
)
```

Parse:

```python
args = parser.parse_args()
```

Access:

```python
args.argument_name
```

---

# 22. Basic `argparse` Mental Model

Remember these four steps:

```text
1. Import
       ↓
import argparse

2. Create parser
       ↓
parser = argparse.ArgumentParser()

3. Add arguments
       ↓
parser.add_argument(...)

4. Parse
       ↓
args = parser.parse_args()
```

Then access the value:

```python
args.team
```

---

# 23. `input()` vs `argparse`

| `input()` | `argparse` |
|---|---|
| Program waits for user input | Input is supplied when starting the program |
| Interactive | Command-line based |
| Simple scripts | Better for reusable CLI tools |
| User types after program starts | User provides values in the command |
| Less convenient for automation | Convenient for automation |
| No automatic CLI help/validation | Provides help and argument validation |

For small interactive programs, `input()` can still be useful.

For reusable command-line tools and automation scripts, `argparse` is often a better fit.

---

# 24. Key Takeaways

### `argparse` is used to:

- accept command-line arguments
- accept positional arguments
- accept optional arguments/flags
- validate input
- provide help messages
- make scripts reusable
- make scripts easier to use from automation

### The most important code:

```python
import argparse

parser = argparse.ArgumentParser(
    description="finds the stadiums"
)

parser.add_argument(
    "team",
    metavar="team",
    type=str,
    help="enter your team"
)

args = parser.parse_args()

team = args.team
```

### Command:

```bash
python workingfile.py arsenal
```

### Flow:

```text
arsenal
   ↓
args.team
   ↓
team
   ↓
API URL
   ↓
requests.get()
   ↓
JSON
   ↓
stadium
```

---

# 25. Hands-On Practice

## Practice 1 — Your Name

Create:

```text
hello.py
```

Run:

```bash
python hello.py Ashraf
```

Expected:

```text
Hello Ashraf
```

### Answer

```python
import argparse

parser = argparse.ArgumentParser(
    description="Print a greeting"
)

parser.add_argument(
    "name",
    type=str,
    help="Enter your name"
)

args = parser.parse_args()

print(f"Hello {args.name}")
```

---

## Practice 2 — Environment

Create a script that accepts:

```bash
python deploy.py dev
```

Expected:

```text
Deploying to dev
```

### Answer

```python
import argparse

parser = argparse.ArgumentParser(
    description="Deployment script"
)

parser.add_argument(
    "environment",
    type=str,
    help="Deployment environment"
)

args = parser.parse_args()

print(f"Deploying to {args.environment}")
```

Try:

```bash
python deploy.py dev
python deploy.py qs
python deploy.py prod
```

---

## Practice 3 — Two Positional Arguments

Create:

```bash
python compare.py dev prod
```

Expected:

```text
Comparing dev with prod
```

### Answer

```python
import argparse

parser = argparse.ArgumentParser(
    description="Compare two environments"
)

parser.add_argument(
    "source",
    type=str,
    help="Source environment"
)

parser.add_argument(
    "target",
    type=str,
    help="Target environment"
)

args = parser.parse_args()

print(f"Comparing {args.source} with {args.target}")
```

---

## Practice 4 — Optional Argument

Create a script that supports:

```bash
python deploy.py --environment dev
```

### Answer

```python
import argparse

parser = argparse.ArgumentParser(
    description="Deployment script"
)

parser.add_argument(
    "-e",
    "--environment",
    type=str,
    help="Deployment environment"
)

args = parser.parse_args()

print(f"Deploying to {args.environment}")
```

---

## Practice 5 — Use `-h`

Take any of the above programs and run:

```bash
python program.py -h
```

Study:

- `usage`
- `description`
- `positional arguments`
- `options`
- your `help` messages

---

# 26. Mini DevOps Exercise

Create:

```text
deploy.py
```

It should accept:

```bash
python deploy.py dev 1.2.3
```

Where:

```text
dev    → environment
1.2.3  → version
```

Expected:

```text
Deploying version 1.2.3 to dev
```

### Solution

```python
import argparse

parser = argparse.ArgumentParser(
    description="Deploy an application"
)

parser.add_argument(
    "environment",
    type=str,
    help="Deployment environment"
)

parser.add_argument(
    "version",
    type=str,
    help="Application version"
)

args = parser.parse_args()

print(
    f"Deploying version {args.version} "
    f"to {args.environment}"
)
```

Try:

```bash
python deploy.py dev 1.2.3
python deploy.py qs 2.0.0
python deploy.py prod 3.1.5
```

---

# 27. Connection to Your DevOps Learning

This basic `argparse` concept is directly relevant to the larger Python script you are studying.

Your Bitbucket automation script uses:

```python
my_parser = argparse.ArgumentParser()
```

and then:

```python
my_parser.add_argument(...)
```

followed by:

```python
args = my_parser.parse_args()
```

Then it uses values such as:

```python
args.branches
args.project
args.secret
args.outputFile
```

So learning this simple example gives you the foundation for understanding that larger automation script.

The next concepts to connect with `argparse` are:

```text
argparse
   ↓
variables
   ↓
strings
   ↓
lists
   ↓
dictionaries
   ↓
functions
   ↓
JSON
   ↓
requests / REST API
   ↓
exception handling
   ↓
file handling
   ↓
concurrency
```

---

# 28. Final Revision

If you remember only these lines, remember:

```python
import argparse

parser = argparse.ArgumentParser(
    description="My program"
)

parser.add_argument(
    "team",
    type=str,
    help="Enter team name"
)

args = parser.parse_args()

team = args.team
```

Run:

```bash
python program.py arsenal
```

Think:

```text
command line
     ↓
"arsenal"
     ↓
argparse
     ↓
args.team
     ↓
team
     ↓
rest of Python program
```

This is the basic foundation of `argparse`.
