# MiniML with Exception Handling

An extension of **MiniML**, the small statically typed functional language from the [Programming Languages Zoo](https://github.com/andrejbauer/plzoo) (by Andrej Bauer and Matija Pretnar; their license is in `LICENSE.txt`). This fork adds division and an exception-handling system with `raise` and `try ... with`, carried through every stage of the compiler.

Course project for Software Development Fundamentals, IIT Hyderabad. The project report is `SDF_Miniml_Project_Report.pdf`.

## What was added

| Stage | File | Change |
|---|---|---|
| Lexer | `src/miniml/lexer.mll` | Tokens for `/`, `try`, `with`, `raise`, `DivisionByZero`, `GenericException`, `{ }` and `\|` |
| Parser | `src/miniml/parser.mly` | Grammar for division, `raise` and `try e1 with <exception> -> e2` |
| AST | `src/miniml/syntax.ml` | `Divide`, `Raise`, `TryWith` and the exception values `DivisionByZero` / `GenericException of int` |
| Type checker | `src/miniml/type_check.ml` | Typing rules for `raise` and `try ... with`, including an analysis of expressions that always raise |
| Compiler | `src/miniml/compile.ml` | Code generation for division, raise and try/with |
| Abstract machine | `src/miniml/machine.ml` | New instructions (`IRaise`, `ITryWith`) and exception values on the stack; division by zero raises `DivisionByZero` at run time |
| Tests | `src/miniml/test_exn.ml` | Regression programs for raising, catching, nested handlers and exceptions from functions |

The original language (integers, booleans, conditionals, recursive functions and top-level definitions) is unchanged.

## Exceptions at a glance

- `DivisionByZero`: raised by `/` when the divisor is zero.
- `GenericException of int`: a user exception carrying an integer.

```
try 10 / 0 with DivisionByZero -> 5 ;;                    (* 5 *)
try raise GenericException(42) with GenericException(42) -> 10 ;;   (* 10 *)
```

## Build and run

Requires OCaml and [Dune](https://dune.build/).

```bash
dune build
python buildRun.py src/miniml/test_exn test_exp
```

`buildRun.py` builds the project with Dune, runs the given MiniML file with `miniml.exe`, and diffs the output against an expected-output file. See its header comment for the exact assumptions.

## Basic Syntax

### Integers
MiniML supports the following integer values:
- `3`
- `42`
- `-7`

### Booleans
MiniML supports two boolean values:
- `true`
- `false`

### Arithmetic Operations
MiniML supports the following arithmetic operations:
- `+` (addition)
- `-` (subtraction)
- `*` (multiplication)
- `/` (division)

#### Example:
- 3 + 4 ;;  // Output: 7
- 10 - 5 ;; // Output: 5
- 3 * 6 ;;  // Output: 18
- 10 / 5 ;; // Output: 2
### Conditionals
The syntax for conditionals is:
if <condition> then <expr> else <expr>if 5 < 10 then 1 else 0 ;; // Output: 1
if 5 < 10 then 1 else 0 ;; // Output: 1
### Functions
You can define recursive functions using the fun keyword. The syntax for defining a function is as follows:
let <function_name> = fun <parameter_name> (<parameter_type>) : <return_type> is <function_body>
let fact = fun f (n : int) : int is
  if n = 0 then 1 else n * f (n - 1) ;;
### Function Application
To apply a function, simply call it with its arguments:
fact 5 ;; // Output: 120
## Exception Handling
### Raising Exceptions
MiniML allows you to raise exceptions using the raise keyword. You can raise two types of exceptions:

#### DivisionByZero:
raise DivisionByZero ;;
#### GenericException(n) where n is an integer (positive or negative):
raise GenericException(42) ;;
raise GenericException(-99) ;;
### Catching Exceptions
To catch exceptions, use the try ... with ... syntax. You specify the code to be executed within the try block and the exception handling in the with block.

#### Basic Example
To catch a DivisionByZero exception:
try 10 / 0 with DivisionByZero -> 5 ;;
This will output 5 as it catches the DivisionByZero exception and returns 5.

#### Catching GenericException
You can also catch a GenericException and handle it:
try raise GenericException(42) with GenericException(42) -> 10 ;;

This will output 10 as it catches the GenericException with value 42 and returns 10.

### Examples
Here are a few examples demonstrating the use of the new exception handling features:

#### Example 1: Safe Division
A division function that handles division by zero:
let safediv = fun f (n : int) : int is
  try 62 / n with DivisionByZero -> -31 ;;
safediv 2 ;;  // Output: 31
safediv 0 ;;  // Output: -31
#### Example 2: Factorial Function with Exception Handling
A factorial function that raises an exception for negative inputs:
let fact = fun f (n : int) : int is
  if n < 0 then raise GenericException(-99)
  else if n = 0 then 1
  else n * f (n - 1) ;;
try fact (-3) with GenericException(-99) -> -99 ;; // Output: -99
This catches the exception for negative numbers and returns -99.
#### Example 3: Nested Exception Handling
Here’s how to handle multiple levels of exceptions:
try
  try raise DivisionByZero with DivisionByZero -> 17
with
  DivisionByZero -> 108 ;;
This example demonstrates nested exception handling. The inner try ... with catches the DivisionByZero exception, and the outer one provides a different handler.
