# 4-bit-processor
This repository contains a **4-bit programmable processor** designed in **Logisim**. It is based on **Von Neumann architecture** and built from the ground up at the gates level. It features a custom instruction set and can load program codes for execution.

![screenshot](https://github.com/riwajp/4-bit-processor/blob/main/screenshots/fibonacci.gif?raw=true)

The above GIF shows a Fibonacci Series program, written in its own assembly code, running on the processor.

---

## Table of Contents
1. [Registers](#registers)
   - [Instruction Register (IR)](#instruction-register-ir)
   - [A Register (Accumulator)](#a-register-accumulator)
   - [D Register](#d-register)
   - [B and C Registers](#b-and-c-registers)
2. [Arithmetic Logic Unit (ALU)](#arithmetic-logic-unit-alu)
   - [Operations](#operations)
   - [Operation Select](#operation-select)
3. [System Bus](#system-bus)
   - [Data Bus](#data-bus)
   - [Address Bus](#address-bus)
4. [Control Unit](#control-unit)
5. [Assembly Instruction Set](#assembly-instruction-set)
   - [ALU Operations](#alu-operations)
   - [Memory Operations](#memory-operations)
   - [Data Movement Operations](#data-movement-operations)
   - [Jump Operations](#jump-operations)
   - [I/O Operations](#io-operations)

---

## Registers

### Instruction Register (IR)
- **Purpose**: Holds the current instruction fetched from RAM for decoding and execution.
- **Behavior**: 
  - Resets to `0` at the first clock signal (`T0`) of every fetch-decode-execute cycle.
  - Cannot be used as a general-purpose register.
- **Special Signals**:
  - `RST` (reset): Clears the content of the IR.

### A Register (Accumulator)
- **Purpose**: 
  - General-purpose register.
  - Used in ALU operations and memory instructions (`STA`, `LDA`).
  
### D Register
- **Purpose**:
  - General-purpose register.
  - Used as the second argument for `ADD` and `SUBTRACT` operations.

### B and C Registers
- **Purpose**: General-purpose registers with no special functionality.

---

## Arithmetic Logic Unit (ALU)

![screenshot](https://github.com/riwajp/4-bit-processor/blob/main/screenshots/alu.png?raw=true)

### Operations
The ALU supports the following operations:

| Operation Type | Instruction  | Description                     |
|----------------|--------------|---------------------------------|
| Arithmetic     | `ADD`, `SUB` | Perform addition or subtraction. |
| Logical        | `NOT`, `AND`, `OR`, `XOR` | Perform bitwise logical operations. |
| Bitwise        | `LSHIFT`, `RSHIFT` | Perform left or right shifts. |

- **Arguments**:  
  - Binary operations (`ADD`, `SUB`): Use `A` and `D` registers.  
  - Unary operations (others): Use `A` register.

### Operation Select
- **Switch Bits**: The lower 3 bits of the opcode (`S3, S2, S1, S0`) determine the operation.

| S3  | S2  | S1  | S0  | Operation  |
|-----|-----|-----|-----|------------|
| 1   | 0   | 0   | 0   | ADD        |
| 1   | 0   | 0   | 1   | SUBTRACT   |
| 1   | 0   | 1   | 0   | COMP       |
| 0   | 0   | 1   | 1   | AND        |
| 1   | 1   | 0   | 0   | OR         |
| 0   | 1   | 0   | 1   | XOR        |
| 1   | 1   | 1   | 0   | LSHIFT     |
| 1   | 1   | 1   | 1   | RSHIFT     |

---

## System Bus

![screenshot](https://github.com/riwajp/4-bit-processor/blob/main/screenshots/system_bus.png?raw=true)

### Data Bus
- A 4-bit data bus handles data transfer between the registers, memory, and ALU.
- **Control Signals**: Select the source of data for the data bus.

| Control Signal | Data Bus Content           |
|----------------|----------------------------|
| `ALU_OP`       | Output from ALU            |
| `ACC_SELECT`   | Content from Accumulator   |
| `MEM_SELECT`   | Content from Memory        |
| `PC_EN`        | Content from Program Counter |
| Otherwise      | Content from selected Register |

#### Register Selection
- **Lower 2 bits of L (address bus)**: Select source register.
- **Higher 2 bits of L**: Generate load (`LD`) signals for destination register.

### Address Bus
- An 8-bit address bus divided into `H` (higher 4 bits) and `L` (lower 4 bits).
- **Address Selection**:
  - If `PC_EN` is high: Address from Program Counter (PC).
  - Else: Address from instruction (`HL`).

---

## Control Unit

![screenshot](https://github.com/riwajp/4-bit-processor/blob/main/screenshots/control_unit.png?raw=true)

The control unit generates control signals to coordinate the operations of the processor.

| Control Signal | Purpose                                                                             |
|----------------|-------------------------------------------------------------------------------------|
| `PC_LD`        | Load data in the address bus into the PC.                                           |
| `ALU_EN`       | Enable ALU output for loading into the Accumulator.                                |
| `PC_EN`        | Enable Program Counter value on the address bus.                                   |
| `MEM_READ`     | Read content from memory at the current address.                                   |
| `MEM_WRITE`    | Write content from data bus into memory.                                           |
| `IR_LD`        | Load content of the data bus into the Instruction Register (IR).                   |
| `AL_LD`        | Load lower 4 bits of address bus (`L`).                                            |
| `AH_LD`        | Load higher 4 bits of address bus (`H`).                                           |
| `ACC_SELECT`   | Select Accumulator content onto the data bus.                                      |
| `ACC_LD`       | Load content from the data bus or ALU output into the Accumulator.                 |
| `PC_CLK`       | Increment PC by 1.                                                                 |
| `T_RST`        | Reset the timing signal to `T0`.                                                   |

---

## Assembly Instruction Set

### ALU Operations
| Instruction | Hex | Binary | Syntax     | Operation                                  |
|-------------|-----|--------|------------|--------------------------------------------|
| ADD         | 8   | 1000   | `ADD`      | Add `A` and `D` registers, store in `A`.   |
| SUB         | 9   | 1001   | `SUB`      | Subtract `D` from `A`, store in `A`.       |
| COMP        | A   | 1010   | `COMP`     | Under development.                         |
| AND         | 3   | 0011   | `AND`      | Bitwise AND of `A` and `D`, store in `A`.  |
| OR          | C   | 1100   | `OR`       | Bitwise OR of `A` and `D`, store in `A`.   |
| XOR         | 5   | 0101   | `XOR`      | Bitwise XOR of `A` and `D`, store in `A`.  |
| LSHIFT      | E   | 1110   | `LSHIFT`   | Left shift `A` register, store in `A`.     |
| RSHIFT      | F   | 1111   | `RSHIFT`   | Right shift `A` register, store in `A`.    |

### Memory Operations
| Instruction | Hex | Binary | Syntax         | Operation                                                                 |
|-------------|-----|--------|----------------|---------------------------------------------------------------------------|
| STA         | D   | 1101   | `STA x y`      | Store Accumulator value in memory at address `yx`.                        |
| LDA         | B   | 1011   | `LDA x y`      | Load memory content at address `yx` into Accumulator.                     |

### Data Movement Operations
| Instruction | Hex | Binary | Syntax     | Operation                                                                          |
|-------------|-----|--------|------------|------------------------------------------------------------------------------------|
| MOV         | 0   | 0000   | `MOV xy`   | Move value from register `x` to register `y`.                                      |
| MVI         | 1   | 0001   | `MVI x`    | Move immediate value `x` into Accumulator.                                         |

### Jump Operations
| Instruction | Hex | Binary | Syntax         | Operation                                                        |
|-------------|-----|--------|----------------|------------------------------------------------------------------|
| JNZ         | 2   | 0010   | `JNZ x y`      | Jump to address `yx` if Zero flag is not set.                    |

### I/O Operations
| Instruction | Hex | Binary | Syntax | Operation                            |
|-------------|-----|--------|------- |--------------------------------------|
| OUT         | 4   | 0100   | `OUT`  | Copy Accumulator content to output.  |






