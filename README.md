#### NAND CADENCE 

---

### **Schematic Diagram (NAND_SCHEMATIC.png)**

This image displays the fundamental **transistor-level schematic** of the 2-input NAND gate. The circuit is designed using complementary metal-oxide-semiconductor (**CMOS**) technology.

* **Pull-up Network (PMOS Transistors):** Two PMOS transistors (**PM0** and **PM1**) are connected in **parallel** between the power supply ($$V_{DD}$$) and the output ($$V_{out}$$). This network's function is to pull the output high (to $$V_{DD}$$) when at least one of the inputs ($$V_{a}$$or$$V_{b}$$) is low.
* **Pull-down Network (NMOS Transistors):** Two NMOS transistors (**NM0** and **NM1**) are connected in **series** between the output ($$V_{out}$$) and ground (**Gnd**). This network pulls the output low when both inputs ($$V_{a}$$and$$V_{b}$$) are high.

This arrangement ensures that the output is only low when both inputs are high, which is the defining characteristic of a NAND gate.

### **Schematic Symbol (NAND_SYMBOL.png)**

The schematic symbol is a simplified, abstract representation of the complex transistor-level circuit. This symbol makes it easier to integrate the NAND gate into larger circuit designs without having to show every transistor.

* **Inputs:** It has two input pins labeled **Va** and **Vb**.
* **Output:** A single output pin is labeled **Vout**.
* **Power and Ground:** The power ($$V_{DD}$$) and ground (**Gnd**) pins are essential for its operation and are included in the symbol.

This abstraction is a standard practice in digital circuit design, promoting modularity and readability.

### **Testbench Setup (NAND_TB.png)**

This image shows the **testbench** used to verify the functionality of the NAND gate. The testbench provides the necessary input signals and power supply for a simulation.

* **Input Stimulus:** Two voltage pulse sources (**V0** and **V1**) are connected to the inputs **Va** and **Vb** of the NAND gate. These sources generate the logical low and high signals to test all possible input combinations (00, 01, 10, 11).
* **Power Supply:** A DC voltage source (**V2**) provides a constant voltage of $$V_{DD}=1$$V to power the gate.
* **Device Under Test (DUT):** The NAND gate symbol (**I0**) is the component being tested, with its pins connected appropriately to the input sources and output probes.

### **Transient Simulation Results (NAND_OUTPUTS.png)**

This image shows the output of a **transient analysis** simulation, which plots the voltage waveforms over time. This is a crucial step to verify the gate's behavior.

* **Input Waveforms:** The top two plots display the input signals for **Va** (red) and **Vb** (green). These waveforms are pulse signals representing logical 0 and 1.
* **Output Waveform:** The bottom plot shows the output signal for **Vout** (magenta).

By observing the output's behavior relative to the inputs, we can confirm the gate's functionality:

* **If Va = 0 AND Vb = 0:** The output **Vout** is high (logic 1).
* **If Va = 0 AND Vb = 1:** The output **Vout** is high (logic 1).
* **If Va = 1 AND Vb = 0:** The output **Vout** is high (logic 1).
* **If Va = 1 AND Vb = 1:** The output **Vout** is low (logic 0).

The simulation results perfectly match the **truth table** for a NAND gate, confirming that the circuit design is correct.



## VERILOG SECTION

## Overview

A **NAND gate** (NOT-AND) is a basic digital logic gate that outputs the negation of the logical AND of its inputs. For two inputs `A` and `B`, the NAND output `Y` is `1` for all input combinations except when both `A` and `B` are `1`.

NAND gates are extremely important because they are **universal** — any other logic gate (AND, OR, NOT, XOR, etc.) can be built solely using NAND gates.

---

## Logic Symbol & Diagram

* The NAND gate can be visualized as an AND gate followed by a small bubble (inverter) on the output.
* In schematics, you will often see a single gate symbol that looks like an AND gate with a small circle (output inversion) at the output.

> Add your project image to `images/nand_diagram.png` and reference it in your repository README if you want the visual to appear on GitHub.

---

## Boolean Expression

For inputs `A` and `B`:

$$
Y = \overline{A \cdot B}
$$

This reads: **Y equals NOT (A AND B)**.

By De Morgan's law:

$$
Y = A' + B'
$$

(Which means NAND output is `1` if either `A` is `0` or `B` is `0`.)

---

## Truth Table

|  A |  B | A·B (AND) | Y = (A·B)' (NAND) |
| -: | -: | :-------: | :---------------: |
|  0 |  0 |     0     |         1         |
|  0 |  1 |     0     |         1         |
|  1 |  0 |     0     |         1         |
|  1 |  1 |     1     |         0         |

---

## How the Circuit Works (Intuitively)

1. The AND portion computes whether both inputs are `1`.
2. The NOT (inverter) flips that result.
3. Therefore, the only case that produces `0` at the NAND output is when both inputs are `1`.

This can also be seen from De Morgan's transformation: NAND is logically equivalent to `A' + B'`.

---

## Implementing Other Gates Using NAND (Universality)

Because NAND is universal, you can construct basic gates as follows (two-input examples):

* **NOT A** using NAND: `A_n = A NAND A` (tie both inputs together).
* **AND(A,B)** using NAND: `AND = (A NAND B) NAND (A NAND B)` — invert the NAND output by NANDing it with itself.
* **OR(A,B)** using NAND: `OR = (A NAND A) NAND (B NAND B)` — using De Morgan.

These building blocks allow implementing complex logic only with NAND gates.

---

## Verilog Implementations

Below are two common ways to describe a 2-input NAND gate in Verilog: structural (using built-in `nand` primitive) and behavioral (using an `assign`).

### Gate-level (structural) Verilog using primitive

```verilog
// verilog/nand_gate_structural.v
module nand_gate (
    output Y,
    input  A,
    input  B
);

    // Built-in primitive: nand
    nand(Y, A, B);

endmodule
```

### Behavioral Verilog (continuous assignment)

```verilog
// verilog/nand_gate_behavioral.v
module nand_gate (
    output Y,
    input  A,
    input  B
);

    // Behavioral description
    assign Y = ~(A & B);

endmodule
```



---

## Testbench (simple)

A compact testbench that exercises all input combinations and prints results:

```verilog
// sim/tb_nand.v
`timescale 1ns/1ps
module tb_nand;
    reg A, B;
    wire Y;

    // Instantiate the NAND gate (change path/name if needed)
    nand_gate uut (.Y(Y), .A(A), .B(B));

    initial begin
        $display("A B | Y (expected)");
        A = 0; B = 0; #5; $display("%b %b | %b (1)", A, B, Y);
        A = 0; B = 1; #5; $display("%b %b | %b (1)", A, B, Y);
        A = 1; B = 0; #5; $display("%b %b | %b (1)", A, B, Y);
        A = 1; B = 1; #5; $display("%b %b | %b (0)", A, B, Y);
        $finish;
    end

endmodule
```






---

## Timing & Electrical Considerations (brief)

* The logical NAND gate description here is idealized and ignores propagation delay.
* In real IC processes, NAND gates have finite propagation delay and output drive limitations. NAND gates are often faster and smaller than equivalent NOR gates in CMOS for certain input counts, but layout and sizing matter.
* For gate-level digital design, consider setup/hold constraints, fan-out, and propagation delay when integrating NAND cells into larger designs.

---

## Applications

* Basic building block in combinational and sequential logic.
* Memory circuits, flip-flops, and multiplexers often use NAND-based implementations.
* Used in programmable logic, FPGAs, and IC standard cell libraries.

---

## Advantages & Disadvantages

**Advantages**

* Universal gate — can implement any boolean function.
* Simple CMOS NAND cell is compact and efficient.
* Widely available in logic families and standard-cell libraries.

**Disadvantages**

* Real circuits must consider delay and electrical characteristics (power, area, noise).
* Implementing some functions only with NANDs may require more gates than a mixed-gate implementation (trade-offs exist).

---

