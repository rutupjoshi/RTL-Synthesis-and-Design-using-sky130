# DAY - 2:Timing libraries, hierarchical vs flat synthesis and efficient flop coding styles

On the second day we learn about the sky130 libraries, hierarchical vs flat synthesis and coding with flip flops.
---

## Contents

- [Timing Libraries](#timing-libraries)
  - [SKY130 PDK](#sky130-pdk-)
  - [Decoding tt_025C_1v80 in the SKY130 PDK](#decoding-tt_025c_1v80-in-the-sky130-pdk)
  - [Opening and understanding the .lib File](#opening-and-understanding-the-lib-file)

- [Hierarchical vs. Flattened Synthesis](#hierarchical-vs-flattened-synthesis)
  - [Hierarchical Synthesis](#hierarchical-synthesis)
  - [Flattened Synthesis](#flattened-synthesis)

- [Flip-Flop Coding Ways](#flip-flop-coding-ways)
  - [Asynchronous Reset D Flip-Flop](#asynchronous-reset-d-flip-flop)
  - [Asynchronous Set D Flip-Flop](#asynchronous-set-d-flip-flop)
  - [Synchronous Reset D Flip-Flop](#synchronous-reset-d-flip-flop)

- [Simulation and Synthesis of flip flop](#simulation-and-synthesis-of-flip-flop)
  - [IVerilog Simulation](#i-verilog-simulation)
  - [Synthesis with Yosys](#synthesis-with-yosys)

---

## SKY130 PDK
Sky130 is an open-source 130-nanometer CMOS Process Design Kit (PDK) created through a partnership between SkyWater Technology Foundry and Google. It provides free, manufacturable semiconductor design resources for chip designers, hobbyists, and universities

---

### Decoding tt_025C_1v80 in the SKY130 PDK

- **tt**: Typical process corner.
- **025C**: Represents a temperature of 25°C, relevant for temperature-dependent performance.
- **1v80**: Indicates a core voltage of 1.8V.

---

## Opening and understanding the .lib File
First open the sky130_fd_sc_hd__tt_025C_1v80.lib file using below command:
```shell
cd ~/sky130RTLDesignAndSynthesisWorkshop/lib
```
Then follow below steps;
1. **Install a text editor:**
   ```shell
   sudo apt install gedit
   ```
2. **Open the file:**
   ```shell
   gedit sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
    
<img width="1920" height="1080" alt="SKY130PDK FILE  lib file" src="https://github.com/user-attachments/assets/65d1e573-2917-4436-ba08-5aa526571bc2" />

---

## Hierarchical vs. Flattened Synthesis:

### Hierarchical Synthesis:

Hierarchical synthesis is a top-down or bottom-up problem-solving method. It breaks a large, complex system into smaller sub-modules. Each part is designed or optimized separately. Finally, the parts are joined to form the complete system.

**Major Concepts** 
**System Breakdown:** Splits a massive task into tiny, manageable pieces.
**Levels of Abstraction:** Moves from high-level goals down to low-level physical parts.
**Independent Optimization:** Solves constraints for each block on its own.
**Integration:** Combines sub-systems into a final working design.

We synthesized the multiple module which explains the hierarchical concept:
<img width="1920" height="1080" alt="hierarchial multiple modules" src="https://github.com/user-attachments/assets/fbdf07a1-42d3-44d4-add6-6607d544818a" />

---

## Flattened Synthesis:
In electronic design automation (EDA) and logic synthesis, flattening (or flat synthesis) is the process of removing module and structural boundaries in a hardware description, turning a multi-level hierarchical netlist into a single, unified block of primitive gates. This allows synthesis tools to optimize global logic across former boundaries, though it can increase runtime and memory usage.

**Pros and Cons**
**Pros:**
Better gate-level optimization, improved timing paths, and smaller area for certain blocks by removing redundant logic.
**Cons:**
Harder debugging due to lost instance names, massive memory footprints on large designs, and longer compilation times.

Example:
<img width="1920" height="1080" alt="flattened synthesis" src="https://github.com/user-attachments/assets/a568d874-350a-44a9-8709-38d8dd430244" />

---

## Flip-Flop Coding Ways :

A flip-flop in digital electronics is a basic memory circuit with two stable states used to store binary data (0 or 1). The main types are SR, D, JK, and T flip-flops. They act as fundamental building blocks for sequential logic.

### Asynchronous Reset D Flip-Flop

```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- **Asynchronous reset**: Overrides clock, setting q to 0 immediately.
- **Edge-triggered**: Captures d on rising clock edge if reset is low.

### Asynchronous Set D Flip-Flop

```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
- **Asynchronous set**: Overrides clock, setting q to 1 immediately.

### Synchronous Reset D Flip-Flop

```verilog
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- **Synchronous reset**: Takes effect only on the clock edge.

---

## Simulation and Synthesis of flip flop
### IVerilog Simulation

1. **Compile:**
   ```shell
   iverilog -o simv dff_asyncres.v tb_dff_asyncres.v
   ```
2. **Run:**
   ```shell
   vvp simv
   ```
3. **View Waveform:**
   ```shell
   gtkwave tb_dff_asyncres.vcd
   ```
The Output:
<img width="1920" height="1080" alt="waveform dff_asuncres simulation" src="https://github.com/user-attachments/assets/ea633399-1a89-4364-8cd5-fb6b12d580b9" />

---

### Synthesis with Yosys
The synthesizing process with yosys includes the same steps as described in day 1 ,except that for a flip flop we include another step between synthesis and technology mapping , which is;

5. Map flip-flops:
   ```shell
   dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

<img width="1920" height="1080" alt="synthesis yosys dff_asynchronus" src="https://github.com/user-attachments/assets/d82fc4b0-abc3-4b6d-b8a7-d91fa1d5217f" />

---
