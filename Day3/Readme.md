# DAY - 3 Combinational and sequential optimizations
On day 3 we learn about combinational and sequential optimizations with examples, its types etc...

---

## Contents
- [1.Why combinational logic optimization?](#1-why-combinatonal-logic-optimization?)
- [2. Constant Propagation](#2-constant-propagation)
- [3. Boolean logic optimization](#3-boolean-logic-optimization)
- [4. Sequential logic optimization](#4-sequential-logic-optimization)
- [5. Sequential constant propagation](#5-sequential-constant-propagation)
- [6. Labs on Optimization](#6-labs-on-optimization)
  - [Lab 1](#lab-1)
  - [Lab 2](#lab-2)
  - [Lab 3](#lab-3)
  - [Lab 4](#lab-4)
  - [Lab 5](#lab-5)
  - [Lab 6](#lab-6)
  - [Lab 7](#lab-7)
  - [Lab 8](#lab-8)
  - [Lab 9](#lab-9)
  - [Lab 10](#lab-10)

---

## 1.Why combinational logic optimization?

**Area Reduction**

**Fewer gates:** Uses a minimal number of logic components (AND, OR, NOT).

**Lower manufacturing cost:** Smaller silicon footprint means more chips fit on a single wafer.

**Reduced wiring complexity:** Fewer physical connections make chip routing easier.

**Performance Improvement**

**Shorter propagation delay:** Signals pass through fewer logic levels, increasing operating speed.

**Higher clock frequencies:** Faster paths allow processors to run at higher clock rates.

**Power Efficiency**

**Less dynamic power:** Fewer switching transitions reduce active energy consumption.

**Reduced heat dissipation:** Cooler chips are more reliable and require less intense cooling systems.

---

## 2. Constant Propagation

Constant optimization in RTL design is a logic reduction technique where synthesis tools or designers identify and remove signals, gates, or registers that evaluate to a fixed logical value (0 or 1). This process simplifies the circuit, cuts dynamic power, and reduces overall silicon area.

**Key Mechanisms of Constant Optimization**

**Constant Folding:** Evaluating operations with static operands directly at compile or synthesis time (e.g., changing 4 + 5 to 9, or 1 & enable to enable).

**Constant Propagation:** Substituting a wire or register pinned to a known value throughout the downstream logic cone.

**Dead Logic Elimination:** Removing redundant logic gates, multiplexer branches, or flip-flops whose outputs no longer affect the primary outputs or state of the design once constants are propagated.

**FSM Optimization:** Simplifying state transition logic when certain state bits or control inputs are tied off to ground or VCC.

---

## 3. Boolean logic optimization
Boolean logic optimization in RTL design is the process of simplifying digital logic expressions derived from Hardware Description Languages (like Verilog or VHDL) before or during synthesis. It transforms generic Boolean networks to minimize area, reduce power consumption, and improve clock speed (timing).

Boolean logic optimization in RTL design reduces the number of logic gates, minimizes propagation delay, and lowers power consumption by simplifying Boolean expressions before technology mapping. During the elaboration and synthesis phases, an EDA tool translates higher-level HDL constructs into technology-independent algebraic expressions. It then applies multi-level or two-level optimization techniques to restructure the underlying logic

---

##  4. Sequential logic optimization

Sequential logic optimization is the process of improving digital circuits that contain memory elements (like flip-flops) and state machines. The main goals are to use less silicon area, draw less power, and run at a faster clock speed while keeping the exact same input-output behavior

**Why It Matters**

**Low Power:** Stops internal parts from switching when values do not change.

**High Speed:** Breaks long paths between registers to raise the maximum clock frequency.

**Small Area:** Shares logic gates and removes unused memory bits.

### Types
state minimization 
state encoding 
retiming 
sequential redundancy removal

---

## 5. Sequential constant propagation
Sequential constant propagation is an advanced logic synthesis optimization that identifies registers (flip-flops) or state elements that permanently hold a fixed value (0 or 1) across clock cycles, propagating those constant values through sequential and combinational logic to eliminate dead hardware

## Working:

**Initial State Analysis:** Tools analyze sequential feedback loops, initial conditions, and tied reset/set pins to find registers that never change state after initialization.

**Fixed-Value Identification:** If a register’s input (D) and feedback paths resolve exclusively to a static logic level, the register output (Q) is declared a sequential constant.

**Value Propagation:** The constant 0 or 1 is pushed forward into downstream combinational gates and finite state machine (FSM) logic cones

---

## 6. Labs on Optimization:

### Lab  1

Verilog code:

```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

Following the same steps from day 1 synthesis lab we add extra line between abc file and synth -top which is:
```shell
opt_clean -purge
```
the output we get is:
<img width="1920" height="1080" alt="optcheck" src="https://github.com/user-attachments/assets/3eedf60f-7ff7-4286-b191-73420c34f7d5" />

---

## Lab 2

Verilog code:

```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="optcheck2" src="https://github.com/user-attachments/assets/8eda90a3-d871-43de-abba-539c5a3964d5" />

---

## Lab 3

Verilog code:

```verilog
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
The synthesis output:
<img width="1920" height="1080" alt="optcheck3" src="https://github.com/user-attachments/assets/5ce4121c-309e-4a77-98e5-e823d8f9c8ee" />

---

## Lab 4

Verilog code:

```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="OPTCHECK4" src="https://github.com/user-attachments/assets/d9712f64-a311-4ab0-bd19-b6120e338fd7" />

---

## Lab 5

Verilog code:

```verilog
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 


endmodule

```

The synthesis output:
<img width="1920" height="1080" alt="multiplemoduleopt" src="https://github.com/user-attachments/assets/5c355dfc-51c4-4610-8455-7d1d9b00879a" />

---

## Lab 6

Verilog code:

```verilog
module sub_module(input a , input b , output y);
 assign y = a & b;
endmodule



module multiple_module_opt2(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module U1 (.a(a) , .b(1'b0) , .y(n1));
sub_module U2 (.a(b), .b(c) , .y(n2));
sub_module U3 (.a(n2), .b(d) , .y(n3));
sub_module U4 (.a(n3), .b(n1) , .y(y));


endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="multiplemodulesopt2" src="https://github.com/user-attachments/assets/70639dee-49bb-44fb-bf6a-299db388d4cf" />

---

## Lab 7

Verilog code:

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="dffconst1" src="https://github.com/user-attachments/assets/178ee404-cb62-485b-9721-da5afe0db65b" />

---

## Lab 8

Verilog code:

```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="dffconst2" src="https://github.com/user-attachments/assets/0d859b74-c53d-401a-acc4-275bfc8253a9" />

---

## Lab 9 

Verilog code:

```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="dffconst3" src="https://github.com/user-attachments/assets/95e19215-e3bf-4661-8e28-528ef98273c3" />

---

## Lab 10

Verilog code:

```verilog
module counter_opt (input clk , input en, input reset , output q);
reg [3:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 4'b0000;
	else if(en)
		count <= count + 1;
end

endmodule
```

The synthesis output:
<img width="1920" height="1080" alt="counteropt" src="https://github.com/user-attachments/assets/f3123005-2b02-42cb-87ae-a57bbfa2c5c4" />

---








