# MARIE Assembly Labs Manual – Labs

This booklet bundles **14 hands‑on labs** that use only MARIE’s *nine basic instructions*:

```
LOAD   STORE   ADD   SUBT   INPUT   OUTPUT   SKIPCOND   JUMP   HALT
```

Students work in MarieSim, MARIE.js, or any compatible simulator.

---
## 📑 How to Use This Manual
1. **Open the simulator** → *File ▸ New* before each lab.
2. **Paste** the code listing, **Assemble**, **Load**, then follow the tasks.
3. Keep the **Show Registers** pane visible—you’ll need it for check‑offs.
4. Submit the deliverables listed at the end of each lab.

> **Tip for instructors:** Merge duplicate constants (e.g. `One`, `Zero`) if you combine multiple labs in one file.

---
## 🗂️ Lab Index
- **Lab 1 - “First Steps & Simple Arithmetic”**
  - 1‑A Echo One Number
  - 1‑B Add Two Numbers
  - 1‑B‑alt Subtract (second − first)
  - 1‑C Double & Negate
  - 1‑D Swap Two Variables
  - 1‑E Absolute Difference
- **Lab 2 – “Loops & Condition Codes”**
  - 2‑A Print 1 … N
  - 2‑B Sum 1 … N
  - 2‑C Integer Multiplication
  - 2‑D Countdown N … 0
  - 2‑E Largest of Two
  - 2‑F Even‑or‑Odd Tester
  - 2‑G Integer Division
  - 2-H Pass/Fail Grade Classifier  

---
# 🟦 Lab 1 - “First Steps & Simple Arithmetic”

## Lab 1‑A  Echo One Number
**Outcome** Run a complete fetch–execute cycle and observe registers.

#### What this lab does
You will write the tiniest possible MARIE program: read one value from the keyboard and immediately print it back. The point is to see each instruction move through the CPU registers.

### Instructions
1. Paste the program below, assemble, then **single‑step** for one positive and one negative input.
2. Record AC after each instruction.

```assembly
/ Echo a single value back to the console
        ORG  100
Start,  INPUT          / AC ← keyboard
        OUTPUT         / AC → console
        HALT
        END
```

### Deliverables
- Table or screenshot showing AC contents per step (both test cases).

#### Code Explanation
`INPUT` copies the typed value into the Accumulator (AC); `OUTPUT` sends AC to the console; `HALT` stops execution. The address field is unused (implied I/O). By stepping you can watch PC, IR, MAR and MBR change and cement the fetch–decode–execute cycle.

---
## Lab 1‑B  Add Two Numbers
**Outcome** Store, load and add memory operands.

#### What this lab does
This program asks for two integers, stores the first in memory, then adds it to the second and prints the sum—your first taste of using RAM as working storage.

### Instructions
1. Test with inputs (3, 5) and (−4, 7). Note any overflow indicator.

```assembly
/ Read two integers and output their sum
        ORG  100
        INPUT          
        STORE Num1     
        INPUT          
        ADD  Num1      
        OUTPUT         
        HALT
Num1,   DEC  0
        END
```

### Deliverables
- Console log for both test pairs.

#### Code Explanation
First `INPUT` fills AC with the first value; `STORE` saves it at label `Num1`. The second `INPUT` places the next value in AC; `ADD Num1` fetches the stored number and adds it to AC. Finally `OUTPUT` prints the result. Notice that without the temporary store the first number would be lost.

---
## Lab 1‑B‑alt  Subtract (second − first)

#### What this lab does
A tiny variation of Lab 1‑B that reverses the arithmetic sign, demonstrating how `SUBT` subtracts the *memory operand* from the AC—not the other way around.

```assembly
/ Read two integers and output (second – first)
        ORG  100
        INPUT
        STORE Num1
        INPUT
        SUBT Num1      / AC = second – first
        OUTPUT
        HALT
Num1,   DEC  0
        END
```

### Deliverables
- Screenshot proving inputs (9, 4) → 5.

#### Code Explanation
The second input is already in AC; `SUBT Num1` performs AC ← AC − Mem[Num1]. Because `SUBT` subtracts *memory from AC*, this yields the desired order: second − first.

---
## Lab 1‑C  Double & Negate
**Outcome** Arithmetic without MUL or NEG.

#### What this lab does
You’ll output twice a number (2 × N) and its negation (−N) using only addition and subtraction—showing that powerful operations can be built from primitives.

### Instructions
1. Predict printed values for input 7 and input −3 before running.
2. Run and compare with predictions.

```assembly
/ Output 2×N and then −N
        ORG  100
        INPUT          / read N
        STORE N
        LOAD  N
        ADD   N        / AC = 2×N
        OUTPUT
        LOAD  Zero
        SUBT N         / AC = −N
        OUTPUT
        HALT
N,      DEC  0
Zero,   DEC  0
        END
```

### Deliverables
- Short write‑up: predicted vs actual values & explanation.

#### Code Explanation
`ADD N` when N is already in AC gives 2 × N. To negate, we load constant 0 and subtract N, producing 0 − N = −N. The constant `Zero` lives in memory for the second part.

---
## Lab 1‑D  Swap Two Variables
**Outcome** Use a temporary variable.

#### What this lab does
Demonstrates a classic three‑step swap pattern using a temp location—mirrors what higher‑level languages generate behind the scenes.

```assembly
/ Swap the order of two inputs, then echo them
        ORG  100
        INPUT
        STORE X
        INPUT
        STORE Y
        LOAD  X
        STORE Temp     / Temp ← X
        LOAD  Y
        STORE X        / X ← Y
        LOAD  Temp
        STORE Y        / Y ← old X
        LOAD  X
        OUTPUT         / print new X
        LOAD  Y
        OUTPUT         / print new Y
        HALT
X,      DEC  0
Y,      DEC  0
Temp,   DEC  0
        END
```

### Deliverables
- Register trace showing values of X and Y before and after swap.

#### Code Explanation
Because MARIE lacks direct register‑to‑register moves, we rely on memory. We save X into `Temp`, overwrite X with Y, then copy `Temp` back into Y. Finally we reload X and Y to print.

---
## Lab 1‑E  Absolute Difference |A − B|
**Outcome** First conditional flow with `SKIPCOND`.

#### What this lab does
Computes the absolute difference between two numbers. If the first subtraction is negative, a branch re‑routes to code that flips the sign.

```assembly
/ Compute |A − B|
        ORG  100
        INPUT
        STORE A
        INPUT
        STORE B

        LOAD  A
        SUBT B          / AC = A−B
        STORE Diff      / save difference

        SKIPCOND 000    / AC < 0 ?
        JUMP  MakePos
Good,   LOAD Diff       / already ≥0
        OUTPUT
        HALT

MakePos,LOAD Zero       / negate Diff
        SUBT Diff       / AC = −(A−B)
        OUTPUT
        HALT

A,      DEC 0
B,      DEC 0
Diff,   DEC 0
Zero,   DEC 0
        END
```

### Deliverables
- Prove correctness with pairs (9, 2) and (2, 9).

#### Code Explanation
After computing A − B we cache it in `Diff`. `SKIPCOND 000` tests if AC < 0; if so we branch to `MakePos`, load 0, subtract `Diff` to reverse the sign, and output. Otherwise we simply reload `Diff` and output.

---
# 🟩 Lab 2 – “Loops & Condition Codes”
### Before you start …
* **Condition selector bits for `SKIPCOND`:**  
  `000` → skip if AC < 0  `400` → skip if AC = 0  `800` → skip if AC > 0  
* A typical loop pattern is:

```assembly
Loop,   ...body...      / do something
        LOAD Counter
        SUBT Limit
        SKIPCOND 800    / skip next JUMP if Counter > Limit
        JUMP Loop
```

## Lab 2‑A  Print 1 … N
**Outcome** Design a counting loop with `SKIPCOND 800`.

#### What this lab does
Builds a rising counter from 1 up to the user‑supplied N, printing each value—your first full while‑style loop.

```assembly
/ Loop from 1 to N, printing each integer
        ORG  100
        INPUT
        STORE Limit       / N
        LOAD  One
        STORE Counter     / Counter = 1

Loop,   LOAD  Counter
        OUTPUT
        ADD   One
        STORE Counter

        LOAD  Counter
        SUBT  Limit
        SKIPCOND 800      / Counter > N ?
        JUMP  Loop
        HALT

Counter,DEC 0
Limit,  DEC 0
One,    DEC 1
        END
```

### Deliverables
- Console snapshot for N = 5 and N = 12.

#### Code Explanation
After printing, we increment `Counter`. The difference Counter − Limit is positive only when Counter > Limit; `SKIPCOND 800` (AC > 0) skips the jump to terminate the loop. Thus the loop runs exactly N times.

---
## Lab 2‑B  Sum 1 … N
**Outcome** Aggregate in memory while looping.

#### What this lab does
Extends Lab 2‑A by keeping a running total in `Sum`, proving how accumulators in memory work alongside the AC.

```assembly
/ Sum integers 1..N and output the total
        ORG  100
        INPUT
        STORE Limit
        LOAD  Zero
        STORE Sum
        LOAD  One
        STORE Counter

Loop,   LOAD  Sum
        ADD   Counter
        STORE Sum

        LOAD  Counter
        ADD   One
        STORE Counter

        LOAD  Counter
        SUBT  Limit
        SKIPCOND 800
        JUMP  Loop

        LOAD  Sum
        OUTPUT
        HALT

Sum,     DEC 0
Counter, DEC 0
Limit,   DEC 0
One,     DEC 1
Zero,    DEC 0
        END
```

### Deliverables
- Verify that N = 10 outputs 55.

#### Code Explanation
Each pass loads `Sum`, adds `Counter`, then stores it back—effectively Sum += Counter. We reuse the same termination test as Lab 2‑A. Finally we load `Sum` and print.

---
## Lab 2‑C  Integer Multiplication (Repeated Addition)
**Outcome** Sentinel‑controlled loop and decrementation.

#### What this lab does
Multiplies X by Y by adding X to `Product` exactly Y times. The loop ends when the countdown `Counter` reaches zero.

```assembly
/ Compute Product = X × Y by repeated addition
        ORG  100
        INPUT
        STORE Multiplicand   / X
        INPUT
        STORE Counter        / Y
        LOAD  Zero
        STORE Product

Loop,   LOAD  Counter
        SKIPCOND 400         / Counter == 0 ?
        JUMP  Done

        LOAD  Product
        ADD   Multiplicand
        STORE Product

        LOAD  Counter
        SUBT  One
        STORE Counter

        JUMP  Loop
Done,   LOAD  Product
        OUTPUT
        HALT

Multiplicand, DEC 0
Counter,      DEC 0
Product,      DEC 0
One,          DEC 1
Zero,         DEC 0
        END
```

### Deliverables
- Demonstrate 6 × 7 → 42 and 5 × 0 → 0.

#### Code Explanation
`SKIPCOND 400` tests AC == 0. If Counter is zero we jump to `Done`. Otherwise we add X into `Product` and decrement Counter (`SUBT One`). This mimics a `while(Counter>0)` loop in high‑level code.

---
## Lab 2‑D  Countdown N … 0
**Outcome** Reverse loop and `< 0` condition.

#### What this lab does
Counts downward from N to zero, stopping once the loop variable becomes negative.

```assembly
/ Print N, N-1, …, 0
        ORG  100
        INPUT
        STORE C

Loop,   LOAD  C
        OUTPUT
        SUBT One
        STORE C

        LOAD  C
        SKIPCOND 000      / AC < 0 ?
        JUMP Loop
        HALT

C,   DEC 0
One, DEC 1
        END
```

### Deliverables
- Output trace for N = 4.

#### Code Explanation
After each print we decrement `C`. When `C` becomes −1 the test `SKIPCOND 000` (AC < 0) triggers, skipping the jump and falling through to `HALT`.

---
## Lab 2‑E  Largest of Two
**Outcome** Single‑branch decision.

#### What this lab does
Chooses the larger of two inputs using one subtraction and one conditional jump—an embryo of an if/else.

```assembly
/ Output the larger of two numbers
        ORG  100
        INPUT
        STORE A
        INPUT
        STORE B

        LOAD  A
        SUBT B
        SKIPCOND 800      / A > B ?
        JUMP  PrintB
        LOAD  A
        OUTPUT
        HALT

PrintB, LOAD  B
        OUTPUT
        HALT

A,  DEC 0
B,  DEC 0
        END
```

### Deliverables
- Show result for (−5, −8) and (2, 9).

#### Code Explanation
If `A − B > 0` (`SKIPCOND 800`) we skip the jump and print A. Otherwise we jump to `PrintB` and output B. Only one branch target is needed.

---
## Lab 2‑F  Even‑or‑Odd Tester
**Outcome** Modulo via repeated subtraction.

#### What this lab does
Determines parity by subtracting 2 until the number is negative, then adds 2 back: AC becomes 0 if even, 1 if odd.

```assembly
/ Output 0 if even, 1 if odd
        ORG  100
        INPUT
        STORE N

Loop,   LOAD  N
        SKIPCOND 000     / AC < 0 ?
        JUMP  Check
        LOAD  N
        SUBT Two
        STORE N
        JUMP  Loop

Check,  LOAD  N
        ADD   Two        / AC = 0 or 1
        OUTPUT
        HALT

N,   DEC 0
Two, DEC 2
        END
```

### Deliverables
- Evidence that inputs 14 and 27 produce 0 and 1 respectively.

#### Code Explanation
The countdown loop keeps subtracting 2. When the result is negative (`SKIPCOND 000`) we exit, then add 2 to restore either 0 (even) or 1 (odd) and print.

---
## Lab 2‑G  Integer Division (Quotient & Remainder)
**Outcome** Dual outputs and inner test on accumulator.

#### What this lab does
Implements long division by repeated subtraction: subtract the divisor from the remainder until negative, counting how many times—yielding both quotient and remainder.

```assembly
/ Divide Dividend by Divisor: output Quotient then Remainder
/ (assumes Divisor ≠ 0)
        ORG  100
        INPUT
        STORE Dividend
        INPUT
        STORE Divisor

        LOAD  Zero
        STORE Quotient
        LOAD  Dividend
        STORE Remainder

Loop,   LOAD  Remainder
        SUBT  Divisor
        SKIPCOND 000          / Remainder < 0 ?
        JUMP  Done
        STORE Remainder       / Remainder -= Divisor

        LOAD  Quotient
        ADD   One
        STORE Quotient

        JUMP  Loop

Done,   LOAD  Quotient
        OUTPUT                / print quotient
        LOAD  Remainder
        OUTPUT                / print remainder
        HALT

Dividend,  DEC 0
Divisor,   DEC 0
Quotient,  DEC 0
Remainder, DEC 0
One,       DEC 1
Zero,      DEC 0
        END
```

### Deliverables
- Show that 17 ÷ 5 yields outputs 3 (quotient) and 2 (remainder).

#### Code Explanation
We keep subtracting `Divisor` from `Remainder`. When the subtraction would go below 0 (`SKIPCOND 000`) we branch to `Done` without storing the negative value. The count of successful subtractions is the quotient; the last non‑negative remainder is already stored.

---

### Extra Lab 2-H Pass/Fail Grade Classifier  
**Goal:** Use a sentinel value to end the program and branch on a *≥ / <* test to print a letter code.

#### What this lab does  
The program repeatedly reads marks in the range 0 – 70.  
* If the mark is **40 or above** it outputs the ASCII letter **“P”** (pass).  
* Otherwise it outputs **“F”** (fail).  
* When the user types **99** (the sentinel) the program halts.

#### MARIE code — nine-instruction subset only
```assembly
/ Pass = 'P', Fail = 'F'.  Stop when the user types 99.
            ORG  100

MainLoop,   INPUT                 / read grade (or 99 to quit)
            STORE Grade

            LOAD  Grade
            SUBT  NinetyNine      / Grade - 99
            SKIPCOND 400          / AC == 0 ?  (sentinel)
            JUMP  StopProgram

            LOAD  Grade
            SUBT  Forty           / Grade - 40
            SKIPCOND 000          / AC < 0 ?   (below 40 → Fail)
            JUMP  PassBranch

FailBranch, LOAD  FLetter         / AC ← 'F' (70)
            OUTPUT
            JUMP  MainLoop

PassBranch, LOAD  PLetter         / AC ← 'P' (80)
            OUTPUT
            JUMP  MainLoop

StopProgram,HALT

/ ---------- data area ----------
Grade       DEC   0
Forty       DEC   40              / pass mark
NinetyNine  DEC   99              / sentinel value
PLetter     DEC   80              / ASCII 'P'
FLetter     DEC   70              / ASCII 'F'
            END
```

#### Code explanation  
1. **Sentinel check**  
   *After storing the input in `Grade`,* the program computes  
   `Grade − 99`.  
   - If the result is zero (`SKIPCOND 400`), we branch to `StopProgram` and halt.  
   - Otherwise execution falls through to the pass/fail test.

2. **Pass / Fail decision**  
   `Grade − 40` sets `AC` negative for scores 0 … 39 and non-negative for 40 … 70.  
   • `SKIPCOND 000` (AC < 0) jumps to the **fail** branch.  
   • If the jump is skipped, control continues to the **pass** branch.

3. **Output**  
   Each branch loads the appropriate ASCII code (`'F'` = 70 or `'P'` = 80) into `AC` and calls `OUTPUT`, then jumps back to `MainLoop` for the next grade.

Run a short test sequence such as **34, 55, 12, 70, 99** and verify that the console prints **F P F P** and then stops.

---
