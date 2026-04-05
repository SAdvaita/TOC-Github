# TOC-Github — Compiler Optimization & Code Generation

> **Course:** Theory of Computation / Compiler Design  
> **Notebook:** `TOC_Github_E0223009.ipynb`  
> **Platform:** Google Colab (Python 3)

---

## Overview

This repository contains a single Jupyter Notebook that demonstrates two fundamental techniques used in the **back-end of a compiler**:

| # | Topic | What it does |
|---|-------|--------------|
| 1 | **Code Optimizer** | Detects and removes redundant computations, performs strength reduction, copy propagation, and dead-code elimination |
| 2 | **Stack-Machine Code Generator** | Translates an infix arithmetic expression into assembly instructions for a stack-based virtual machine |

---

## Program 1 — Code Optimizer

### Problem Statement

> *Implement a program that detects redundant computations, dead code, and strength reduction.*

### How it works

The optimizer processes a list of simple arithmetic assignment statements (e.g. `x = 2 * 8`) through **three sequential phases**:

#### Phase 1 — Expression Analysis
Inspects each assignment and applies local optimizations:

| Technique | Trigger | Example |
|-----------|---------|---------|
| **Constant Folding / Strength Reduction** | Both operands are integer literals | `x = 2 * 8` → `x = 16` |
| **Identity Elimination (× 1)** | Multiplying by `1` | `y = x * 1` → `y = x` |
| **Identity Elimination (+ 0)** | Adding `0` | `z = y + 0` → `z = y` |

#### Phase 2 — Copy Propagation
Iteratively replaces intermediate copies with their ultimate source.  
Example: after Phase 1 we have `z = y` and `y = x`; propagation turns `z = y` into `z = x`.

#### Phase 3 — Dead Code Elimination
Performs a backwards liveness analysis starting from the last (output) variable.  
Any variable that is not reachable from the output is removed from the final code.

### Example

**Input**
```
x = 2 * 8
y = x * 1
z = y + 0
```

**Optimized Output**
```
x = 16
z = x
```
*(The intermediate copy `y` is dead and is eliminated.)*

### Supported Statement Syntax

```
<variable> = <operand1> <operator> <operand2>
```

- Operators: `+`, `-`, `*`, `/`
- Operands: integer literals or single variable names

### Running the Optimizer

```python
from optimize_code import optimize_code   # or run the notebook cell directly

lines = ["x=2*8", "y=x*1", "z=y+0"]
result = optimize_code(lines)
# result → ['x = 16', 'z = x']
```

When run interactively (as in the notebook), the program prompts for input line by line and prints each phase's output.

---

## Program 2 — Stack-Machine Code Generator

### Problem Statement

> *Implement a simple code generator that translates arithmetic expressions into target assembly for a stack machine.*

### How it works

The generator converts an **infix** arithmetic expression into **postfix** (Reverse Polish Notation) using the classic **Shunting-Yard Algorithm**, then emits one assembly instruction per postfix token.

#### Step 1 — Infix → Postfix (Shunting-Yard)

Operator precedence table used:

| Operator | Precedence |
|----------|-----------|
| `+`, `-` | 1 (low) |
| `*`, `/` | 2 (high) |

Parentheses are handled correctly to override default precedence.

#### Step 2 — Postfix → Assembly

| Postfix Token | Assembly Instruction |
|---------------|---------------------|
| operand (variable or number) | `PUSH <operand>` |
| `+` | `ADD` |
| `-` | `SUB` |
| `*` | `MUL` |
| `/` | `DIV` |

### Example

**Input expression:** `(a + b) * c`

**Intermediate postfix:** `a b + c *`

**Generated assembly:**
```
PUSH a
PUSH b
ADD
PUSH c
MUL
```

### Running the Code Generator

```python
from stack_codegen import generate_stack_code   # or run the notebook cell directly

code = generate_stack_code("(a+b)*c")
# code → ['PUSH a', 'PUSH b', 'ADD', 'PUSH c', 'MUL']
```

When run interactively, the program asks for the expression and prints both the intermediate postfix form and the final assembly listing.

---

## Repository Structure

```
TOC-Github/
│
├── TOC_Github_E0223009.ipynb   # Main notebook (both programs)
└── README.md                   # This file
```

---

## Prerequisites

| Requirement | Version |
|-------------|---------|
| Python | 3.10+ |
| Jupyter / Google Colab | any recent version |

No external libraries are required — only the Python standard library (`re` module).

---

## How to Run

### Option A — Google Colab (recommended)
1. Open the `.ipynb` file in [Google Colab](https://colab.research.google.com/).
2. Run each cell from top to bottom (`Runtime → Run all`).
3. Follow the interactive prompts for input.

### Option B — Local Jupyter
```bash
pip install notebook
jupyter notebook TOC_Github_E0223009.ipynb
```

### Option C — Plain Python (extract the source)
Copy the source code from either cell and save it as a `.py` file, then:
```bash
python optimizer.py
# or
python stack_codegen.py
```

---

## Key Concepts

| Term | Definition |
|------|-----------|
| **Constant Folding** | Evaluating constant expressions at compile time rather than runtime |
| **Strength Reduction** | Replacing expensive operations with cheaper equivalents (e.g. multiply by constant → addition) |
| **Identity Elimination** | Removing operations whose result is trivially the same as an operand (`x * 1 = x`, `x + 0 = x`) |
| **Copy Propagation** | Replacing a copy variable with the original variable throughout the code |
| **Dead Code Elimination** | Removing assignments to variables whose values are never used |
| **Shunting-Yard Algorithm** | Dijkstra's algorithm for converting infix expressions to postfix in O(n) |
| **Stack Machine** | A virtual machine where all operations read from and write to an operand stack |

---

## Author

**Roll Number:** E0223009  
Submitted as part of a Theory of Computation / Compiler Design course assignment.
