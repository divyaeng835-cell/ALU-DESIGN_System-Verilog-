# Experiment 1: ALU Design using Enumerated Data Types and Case Statements

---  

## Aim  
To design and simulate a **4-bit Arithmetic Logic Unit (ALU)** using **SystemVerilog HDL** with **Enumerated Data Types and Case Statements**, and verify its functionality using **Synopsys VCS and DVE**.

---
 
## Apparatus Required  
- Computer with **Windows** OS  
- **Synopsys VCS and DVE** (or later) installed  
- SystemVerilog source code editor  

---

## Description about ALU  
An **Arithmetic Logic Unit (ALU)** is a combinational circuit that performs arithmetic and logical operations.  
In this experiment, the ALU is designed using:  
- **Enumerated Data Types** to represent ALU operations in a readable form.  
- **Case Statements** to implement the functionality of each operation.  

This approach improves code readability, maintainability, and reduces errors compared to using raw binary values for control signals.  

Common ALU operations included in this design are:  
- **Arithmetic:** Addition, Subtraction  
- **Logical:** AND, OR, XOR, NOT  
- **Shift Operations:** Left Shift, Right Shift  

---

## Features
- Uses **SystemVerilog Enumerated Types** for operation selection  
- Implements ALU operations with **Case Statements**  
- Parameterized design for scalability  
- Includes a **Testbench** for functional verification  
- Compatible with **Synopsys VCS and DVE**  

---
# Procedure

1. **Create a New Project Folder**
   - Create a new folder (e.g., `ALU_Enum_Project`).
   - Open the folder.
   - Right-click inside the folder and select **Open in Terminal**.

2. **Create SystemVerilog Source Files**
   - Create the ALU design file:
     ```bash
     gedit alu_enum.sv
     ```
   - Paste the ALU design code into the file, then save and close it.

   - Create the testbench file:
     ```bash
     gedit alu_enum_tb.sv
     ```
   - Paste the testbench code into the file, then save and close it.

3. **Verify the Files**
   - Ensure the following files are present in the project folder:
     - `alu_enum.sv`
     - `alu_enum_tb.sv`

4. **Set Up the Synopsys Environment**
   - Initialize the Synopsys environment by running:
     ```bash
     source /synopsys/start.sh
     ```

5. **Compile the Design and Testbench**
   - Compile both SystemVerilog files using VCS:
     ```bash
     vcs -full64 -sverilog alu_enum.sv alu_enum_tb.sv
     ```
   - Ensure the compilation completes without any syntax errors.

6. **Run the Simulation**
   - Execute the generated simulation executable:
     ```bash
     ./simv
     ```
   - The simulation will generate the waveform database (if `$dumpfile` and `$dumpvars` are included in the testbench).

7. **Open the DVE Waveform Viewer**
   - Launch the DVE waveform viewer:
     ```bash
     dve -full64
     ```

8. **View the Waveforms**
   - In the DVE window:
     - Go to **File → Open Database** (or **File → New Database**, depending on the DVE version).
     - Open the generated simulation database.
     - Add the required signals (`A`, `B`, `operation`, `ALU_Out`, and `CarryOut`) to the waveform window using **Add to Waves**.

9. **Analyze the Results**
   - Verify the outputs for each ALU operation:
     - Addition (`ADD`)
     - Subtraction (`SUB`)
     - AND (`AND`)
     - OR (`OR`)
     - XOR (`XOR`)
     - NOT (`NOT`)
     - Shift Left (`SHL`)
     - Shift Right (`SHR`)

10. **Save the Waveform**
    - Save the waveform session for future reference or documentation if required.

---


## SystemVerilog Code  

### ALU Design (`alu_enum.sv`)
```systemverilog
// -----------------------------------------
    // Define Enumerated Data Type for ALU Ops
    // -----------------------------------------
    typedef enum logic [2:0] {
        ADD = 3'b000,
        SUB = 3'b001,
        AND_OP = 3'b010,
        OR_OP  = 3'b011,
        XOR_OP = 3'b100,
        NOT_OP = 3'b101,
        SHL    = 3'b110,
        SHR    = 3'b111
    } alu_ops_t;
// ========================================================
// ALU Design using Enumerated Data Types and Case Statements
// ========================================================

module alu_enum #(parameter WIDTH = 4) (
    input  logic [WIDTH-1:0] A, B,
    input  alu_ops_t operation,      // Enumerated operation selector
    output logic [WIDTH-1:0] ALU_Out,
    output logic CarryOut
);

    // -----------------------------------------
    // Internal signal
    // -----------------------------------------
    logic [WIDTH:0] tmp;

    // -----------------------------------------
    // ALU operation using case statement
    // -----------------------------------------
    always_comb begin
        tmp = '0;

        case (operation)
            ADD    : tmp = A + B;
            SUB    : tmp = A - B;
            AND_OP : tmp = A & B;
            OR_OP  : tmp = A | B;
            XOR_OP : tmp = A ^ B;
            NOT_OP : tmp = ~A;
            SHL    : tmp = A << 1;
            SHR    : tmp = A >> 1;
            default: tmp = '0;
        endcase
    end

    assign ALU_Out  = tmp[WIDTH-1:0];
    assign CarryOut = tmp[WIDTH];

endmodule
```
---

### ALU Testbench (`alu_tb.sv`)
```systemverilog
// ========================================================
// Testbench for ALU using Enumerated Data Types
// ========================================================

module alu_enum_tb;

    // -----------------------------------------
    // Testbench signals
    // -----------------------------------------
    logic [3:0] A, B;
    alu_ops_t operation;
    logic [3:0] ALU_Out;
    logic CarryOut;

    // -----------------------------------------
    // Instantiate ALU
    // -----------------------------------------
    alu_enum #(4) uut (
        .A(A),
        .B(B),
        .operation(operation),
        .ALU_Out(ALU_Out),
        .CarryOut(CarryOut)
    );

    // -----------------------------------------
    // Apply test vectors
    // -----------------------------------------
    initial begin

        A = 4'b0010; B = 4'b1000; operation = ADD;    #20;
        A = 4'b0101; B = 4'b0011; operation = SUB;    #20;
        A = 4'b1010; B = 4'b1100; operation = AND_OP; #20;
        A = 4'b1010; B = 4'b1100; operation = OR_OP;  #20;
        A = 4'b1010; B = 4'b1100; operation = XOR_OP; #20;
        A = 4'b1010; B = 4'b0000; operation = NOT_OP; #20;
        A = 4'b0001; B = 4'b0100; operation = SHL;    #20;
        A = 4'b0110; B = 4'b0010; operation = SHR;    #20;

        $finish;

    end
    initial begin
        $dumpfile("alu_enum.vcd");
        $dumpvars(0,alu_enum_tb);
    end
endmodule
```
---

### Simulation Output

The simulation is carried out using ModelSim 2020.1.

<img width="1920" height="1080" alt="Screenshot 2026-07-22 090605" src="https://github.com/user-attachments/assets/dc21e75e-d8c2-43c5-8ff3-80ce387f2113" />


---
# Output Explanation

The simulation waveform verifies the functionality of the **SystemVerilog ALU** implemented using **Enumerated Data Types (`typedef enum`)** and **case statements**. The inputs `A`, `B`, and `operation` are varied in the testbench to perform different arithmetic, logical, and shift operations. The resulting outputs are observed through `ALU_Out` and `CarryOut`.

---

## Waveform Analysis

| Time (ns) | A (Hex) | B (Hex) | Operation | Operation Name | ALU_Out | CarryOut | Explanation |
|-----------|----------|----------|-----------|----------------|---------|----------|-------------|
| 0 – 20 | `2` | `8` | `0` | **ADD** | `A` | `0` | Performs addition: `2 + 8 = 10 (0xA)`. Since the result fits within 4 bits, `CarryOut = 0`. |
| 20 – 40 | `5` | `3` | `1` | **SUB** | `2` | `0` | Performs subtraction: `5 - 3 = 2`. No borrow is generated. |
| 40 – 60 | `A` | `C` | `2` | **AND** | `8` | `0` | Bitwise AND operation: `1010 & 1100 = 1000 (0x8)`. |
| 60 – 80 | `A` | `C` | `3` | **OR** | `E` | `0` | Bitwise OR operation: `1010 \| 1100 = 1110 (0xE)`. |
| 80 – 100 | `A` | `C` | `4` | **XOR** | `6` | `0` | Bitwise XOR operation: `1010 ^ 1100 = 0110 (0x6)`. |
| 100 – 120 | `A` | `0` | `5` | **NOT** | `5` | `1` | Bitwise complement of `A`: `~1010 = 0101 (0x5)`. The MSB of the temporary result appears as `CarryOut = 1`. |
| 120 – 140 | `1` | `4` | `6` | **SHL** | `2` | `0` | Left shift operation: `0001 << 1 = 0010 (0x2)`. |

---

## Signal Description

- **A** : First 4-bit operand.
- **B** : Second 4-bit operand.
- **operation** : Enumerated selector that determines the ALU function.
- **ALU_Out** : 4-bit output of the selected ALU operation.
- **CarryOut** : Carry bit generated during arithmetic operations (or the extra MSB stored in the temporary result).

---

## Enumerated Operation Codes

| Operation Code | ALU Operation |
|----------------|---------------|
| `0` | ADD |
| `1` | SUB |
| `2` | AND |
| `3` | OR |
| `4` | XOR |
| `5` | NOT |
| `6` | SHL (Shift Left) |
| `7` | SHR (Shift Right) |

---

### Result

The design and simulation of a 4-bit ALU using Enumerated Data Types and Case Statements in SystemVerilog HDL was successfully carried out in Synopsys VCS and DVE.
The ALU performed arithmetic, logical, and shift operations correctly as verified from the simulation outputs.

