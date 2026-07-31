# DAY 4 : GLS, blocking vs non-blocking and Synthesis-Simulation mismatch
On the fourth day we were taught of Gate level simulation ,meaning of blocking and non blocking statements and examples of synthesis - Simulation mismatch.

---

## Contents.

- [1. Gate-Level Simulation (GLS)](#1-gate-level-simulation-gls)
- [2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
- [3. Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)
  - [Blocking Statements](#31-blocking-statements)
  - [Non-Blocking Statements](#32-non-blocking-statements)
- [4. Labs](#4-labs)

---

## 1. Gate-Level Simulation (GLS)
Gate-level simulation (GLS) in RTL design is the verification of a synthesized structural netlist (composed of actual logic cells like AND, OR, and flip-flops) using a testbench. It validates that functionality is preserved after synthesis, checks real physical timing delays via Standard Delay Format (SDF) back-annotation, and captures switching activity for accurate power analysis.

**Why GLS is Needed in the Design Flow**

**Timing & Glitch Verification:** Static Timing Analysis (STA) covers clock-to-data paths, but GLS exposes real hardware glitches, asynchronous race conditions, and overlapping clock domain interactions.

**X-Propagation Analysis:** Identifies uninitialized storage elements or unknown states (X) that behave optimistically in RTL simulation but lock up or corrupt physical hardware.

**Power-Intent and DFT Check:** Validates low-power control structures (retention flops, isolation cells) and Design-for-Test (DFT) scan chains added post-RTL.

---

## 2. Synthesis-Simulation Mismatch

A synthesis simulation mismatch happens when your RTL behavior in a pre-synthesis functional simulation differs from the behavior of the generated gate-level netlist. This occurs when code contains ambiguities or constructs that a simulator interprets behaviorally using time or states, while a synthesis tool maps them into unintended hardware.

**Common Causes of Mismatches**

**Incomplete Sensitivity Lists:** Missing signals in combinational always or process blocks cause simulators to miss updates, whereas synthesis tools infer pure combinational logic.

**Inferred Latches:** Failing to cover all branches in if-else or case statements forces the compiler to build unintended memory latches.

**Blocking vs. Non-Blocking Assignments:** Mixing assignment types inside sequential and combinational blocks creates severe simulation race conditions.

**Synthesis Directives / Full-Case & Parallel-Case:** Using unsafe attributes tells the synthesis tool to optimize logic paths that the simulator does not recognize, creating a blind spot.

**Initial Blocks and Delays:** Writing code with explicit #delay components or initial preset values that simulate fine but get completely ignored by hardware physical mapping.

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog

 ### Blocking Statements
 A blocking assignment in Verilog uses the = operator and executes sequentially within a structured block. It evaluates the right-hand side (RHS) expression and immediately updates the left-hand side (LHS) variable before allowing any subsequent lines of code in that block to execute. This behavior blocks the flow of execution, mimicking how traditional software programming languages operate.

**Key Characteristics**

**Operator:** Represented by a single equals sign (=).

**Immediate Update:** The target variable is updated instantly during the current evaluation step. 

**Sequential Execution:** Lines of code execute one after the other in strict order.

**Scope Restriction:** It only blocks execution within its own procedural block (e.g., initial or always). It does not block other parallel blocks running concurrently in the design. 

**Example of Blocking Assignments**

 In the following example, the variables are updated in a step-by-step sequence:
 
 ```verilog
always @* begin
    x = a & b;   // 1. Evaluates a & b, updates x immediately
    y = x | c;   // 2. Uses the newly updated x to compute y
end
```
Because x updates instantly, line 2 uses the new value of x to calculate y. This maps perfectly to combinational logic gates in hardware.

### Non Blocking Statements
A non-blocking statement in Verilog uses the <= operator to schedule assignments at the end of a time-step without blocking the execution of subsequent statements. This allows multiple operations to execute concurrently, safely mimicking how physical hardware registers update simultaneously on a clock edge.

**How It Works**

**Two-step execution:** First, it evaluates the right-hand side (RHS) values for all statements in the block. Second, it updates the left-hand side (LHS) variables at the end of the simulation time step.

**No blocking:** Other statements in the same block do not wait for the assignment to finish updating the variable before they execute.

**Concurrency model:** It mimics parallel hardware behavior, such as multiple flip-flops triggering on the same clock edge.


**Practical Example: Shifting Data**

The code below shows why non-blocking statements are critical for describing real hardware pipeline registers without creating race conditions.

```verilog
always @(posedge clk) begin
    b <= a; // Evaluates current 'a', schedules update for 'b'
    c <= b; // Evaluates current 'b' (old value), schedules update for 'c'
end
```
**Result:**
b gets the value of a, and c gets the previous value of b. This correctly creates a 2-stage shift register (two flip-flops in series).

---

## 4. Labs
### Lab 1 : Ternary operator mux

Verilog code for ternary operator mux

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
The waveform:
<img width="1920" height="1080" alt="waveform ternary operator mux" src="https://github.com/user-attachments/assets/df5f161f-30b2-44d6-bfa4-5ff833ee4603" />

The above program when synthesized with yosys gives the below output:
<img width="1920" height="1080" alt="ternarymux SYNTHESIS yosys result" src="https://github.com/user-attachments/assets/527290c1-2ef1-4e68-af4f-91cd37ef545d" />

---

## Lab 2 : Blocking Caveat

Verilog code for blocking caveat:

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

<img width="1920" height="1080" alt="blocking caveat waveform" src="https://github.com/user-attachments/assets/f4a21cc1-c721-4200-ba0a-f6b14c100e55" />

Synthesis of blocking caveat module:

<img width="1920" height="1080" alt="blocking caveat synthesis" src="https://github.com/user-attachments/assets/1261c09d-276f-4482-85e3-2c92e3d52ed1" />

---

## Lab 3 : Bad MUX

Verilog code with intentional issues:

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```

### Errors:
-  Should include `i0`, `i1`, and `sel`.
-  Should use blocking assignments (`=`).

**Corrected version:**
```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

<img width="1920" height="1080" alt="badmux waveform corrected version" src="https://github.com/user-attachments/assets/9330c923-021c-434e-b8c3-bbbf2b88c7eb" />

---

