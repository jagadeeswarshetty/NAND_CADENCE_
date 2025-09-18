
# 2-Input NAND Gate (CMOS + Verilog)

This repository documents the **design, schematic, simulation, and Verilog implementation** of a 2-input NAND gate.  
Both **transistor-level CMOS design (Cadence Virtuoso)** and **Verilog HDL** are included.

---

## CMOS Design in Cadence

### Schematic Diagram (NAND_SCHEMATIC.png)

The transistor-level schematic of a 2-input NAND gate, built using CMOS technology:

- **Pull-up (PMOS):** Two PMOS transistors (PM0, PM1) connected in parallel between `VDD` and `Vout`.  
- **Pull-down (NMOS):** Two NMOS transistors (NM0, NM1) connected in series between `Vout` and ground.  

This configuration ensures the output is **low only when both inputs are high**.

---

### Schematic Symbol (NAND_SYMBOL.png)

A simplified symbol representing the NAND gate for modular use in larger designs.  

- **Inputs:** `Va`, `Vb`  
- **Output:** `Vout`  
- **Power Pins:** `VDD`, `Gnd`  

---

### Testbench Setup (NAND_TB.png)

The testbench applies input signals and power to verify the gate:

- **Inputs:** Voltage pulse sources `V0` and `V1` (generating 00, 01, 10, 11).  
- **Power:** DC source `V2` with `VDD = 1V`.  
- **Device Under Test:** NAND gate symbol (`I0`).  

---

### Transient Simulation Results (NAND_OUTPUTS.png)

- **Inputs:** `Va` (red), `Vb` (green)  
- **Output:** `Vout` (magenta)  

#### Verified Truth Table

| Va | Vb | Vout |
|----|----|------|
| 0  | 0  | 1    |
| 0  | 1  | 1    |
| 1  | 0  | 1    |
| 1  | 1  | 0    |

---

### Verilog Section

### Boolean Expression

For inputs `A` and `B`:

$$
Y = \overline{A \cdot B}
$$

---

### Gate Symbol

The NAND gate is an **AND gate followed by an inverter bubble** on the output.

---

### Verilog Implementations

#### Gate-Level (Structural)

```verilog
module nand_gate (
    output Y,
    input  A,
    input  B
);
    nand(Y, A, B); // Built-in primitive
endmodule
````

#### Behavioral

```verilog
module nand_gate (
    output Y,
    input  A,
    input  B
);
    assign Y = ~(A & B);
endmodule
```

---

### Testbench

```verilog
`timescale 1ns/1ps
module tb_nand;
    reg A, B;
    wire Y;

    nand_gate uut (.Y(Y), .A(A), .B(B));

    initial begin
        $display("A B | Y");
        A=0; B=0; #5; $display("%b %b | %b", A, B, Y);
        A=0; B=1; #5; $display("%b %b | %b", A, B, Y);
        A=1; B=0; #5; $display("%b %b | %b", A, B, Y);
        A=1; B=1; #5; $display("%b %b | %b", A, B, Y);
        $finish;
    end
endmodule
```

---

## Applications

* Fundamental building block in digital systems.
* Basis for memory circuits, flip-flops, and multiplexers.
* NAND is **universal** — any Boolean function can be implemented using NAND gates.

---

## Advantages & Disadvantages

**Advantages**

* Universal gate, compact CMOS design.
* Low power consumption, high noise immunity.

**Disadvantages**

* Using only NANDs may require more gates than mixed implementations.
* Real-world propagation delays must be considered in timing analysis.
