# μFé-8 EX (Extended Microcontroller Architecture)

An independent extension and enhanced implementation of **μFé-8** 8-bit microcontroller architecture. This project serves as a practical link between fundamental concepts of **Digital Systems** and **Microprocessor Systems**.

> **Credits & Acknowledgments**: This project is based on the original specification and open-source architecture created by [@dccafe in the uFe8 project](https://github.com/dccafe/uFe8)[cite: 1].

---

## 📌 Architecture Overview

The **μFé-8 EX** is an 8-bit microcontroller inspired by a simplified subset of the MSP430 architecture.

* **Data & Address Bus Width**: 8-bit
* **General-Purpose Registers**: `R0`, `R1`, `R2`, `R3`
* **Internal Special Registers**:
  * `PC` (Program Counter)[cite: 1]
  * `IR` (Instruction Register)[cite: 1]
  * `CTE` (Constant Register)[cite: 1]
  * **`SP` (Stack Pointer)** *(Extension added in this version)*

### 🗺️ Memory Map

| Address Range | Region | Description |
|---|---|---|
| `0x00` - `0x7F` | **ROM** | Program instructions and constants[cite: 1] |
| `0x80` - `0xBF` | **RAM** | General data variables and System Stack[cite: 1] |
| `0xC0` - `0xFF` | **Peripherals** | Hardware I/O and custom peripherals (GPIO, Timers)[cite: 1] |

---

## 🚀 Architectural Extension: Subroutine & Stack Support (`CALL` / `RET`)

In this extended version, the original architecture was upgraded to natively support **subroutine/function calls** using a hardware-managed stack located in RAM.

### 1. Stack Pointer (`SP`)
* An 8-bit register initialized at the top of RAM (`0xBF`)[cite: 1].
* Decrements on push operations (saving context) and increments on pop operations (restoring context).

### 2. New Subroutine Instructions

| Mnemonic | Opcode Format | Operation | Description |
|---|---|---|---|
| `CALL label` | `111 1 xx 100` | `RAM[SP] <= PC + 2`<br>`SP <= SP - 1`<br>`PC <= label` | Pushes the return address onto the stack and jumps to the subroutine. |
| `RET` | `111 1 xx 101` | `SP <= SP + 1`<br>`PC <= RAM[SP]` | Pops the return address from the stack and returns to the main execution flow. |

---

## 🏗️ Base Instruction Set Architecture (ISA)

Instructions are encoded in 8 bits (1 byte)[cite: 1]. An optional 8-bit immediate byte follows the instruction if the constant flag (`cte`) is set to `1`[cite: 1].

| Opcode | C | SRC | DST | Mnemonic | Details |
|---|---|---|---|---|---|
| `000` | c | ss | dd | `ADD Rs/#i, Rd` | `[Rs\|#i] + Rd => Rd`[cite: 1] |
| `001` | c | ss | dd | `AND Rs/#i, Rd` | `[Rs\|#i] & Rd => Rd`[cite: 1] |
| `010` | c | ss | dd | `XOR Rs/#i, Rd` | `[Rs\|#i] ^ Rd => Rd`[cite: 1] |
| `011` | c | ss | dd | `OR  Rs/#i, Rd` | `[Rs\|#i] \| Rd => Rd`[cite: 1] |
| `100` | c | ss | dd | `LD  @Rs/@i, Rd` | `@Rs/@i => Rd`[cite: 1] |
| `101` | c | ss | dd | `ST  Rs/#i, @Rd` | `Rs/#i => @Rd`[cite: 1] |
| `110` | c | ss | dd | `MOV Rs/#i, Rd` | `[Rs\|#i] => Rd`[cite: 1] |
| `111` | 1 | xx | 00 | `JC label` | `PC <= label` if `C == 1`[cite: 1] |
| `111` | 1 | xx | 01 | `JZ label` | `PC <= label` if `Z == 1`[cite: 1] |
| `111` | 1 | xx | 10 | `JNZ label` | `PC <= label` if `Z == 0`[cite: 1] |
| `111` | 1 | xx | 11 | `JMP label` | `PC <= label`[cite: 1] |

---

## 🛠️ Repository Structure

```text
ufe8-ex/
├── docs/               # Technical specifications and Control Unit (FSM) state diagrams
├── logisim/            # Logisim/Digital schematic circuit designs (.circ)
├── rtl/                # SystemVerilog/VHDL design modules (optional)
├── sim/                # Simulation testbenches
├── tools/              # Custom Python Assembler to generate ROM .hex files
├── .gitignore
├── LICENSE
└── README.md
