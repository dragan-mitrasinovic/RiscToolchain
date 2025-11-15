# Assembler-Linker-Emulator

[![Language](https://img.shields.io/badge/language-C++-blue.svg)](https://isocpp.org/)
[![Build Tools](https://img.shields.io/badge/build-Make-green.svg)](https://www.gnu.org/software/make/)
[![Parser](https://img.shields.io/badge/parser-Bison%20%2B%20Flex-orange.svg)](https://www.gnu.org/software/bison/)

A complete toolchain for assembling, linking, and emulating programs written in a custom assembly language for a simplified RISC-like CPU architecture.

> 🎓 Developed as a project for System Programming (13E113SS) at the University of Belgrade, School of Electrical Engineering

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Building](#building)
- [Usage](#usage)
  - [Assembler](#assembler)
  - [Linker](#linker)
  - [Emulator](#emulator)
- [Assembly Language Syntax](#assembly-language-syntax)
- [Example](#example)

## 🔍 Overview

This project implements a complete system software toolchain consisting of three main components:

1. **Assembler** - Translates assembly source code into relocatable object files
2. **Linker** - Combines multiple object files into a single executable with resolved symbols and relocated addresses
3. **Emulator** - Simulates the execution of the linked program on a virtual processor

The implementation demonstrates core concepts in systems programming including:
- Two-pass assembly
- Symbol resolution and relocation
- Memory management
- Instruction encoding and decoding
- Interrupt handling
- Stack-based calling conventions

## ✨ Features

### Assembler
- **Two-pass assembly** for forward symbol references
- **Lexical analysis** using Flex
- **Syntax parsing** using Bison
- **Symbol table** management with global and external symbols
- **Section-based** code organization
- **Literal and symbol pools** for efficient addressing
- **Relocation records** generation
- Support for **multiple addressing modes**

### Linker
- **Symbol resolution** across multiple object files
- **Section placement** at specified memory addresses using `-place` option
- **Relocation processing** to fix up addresses
- **Hex file output** format for emulator
- **Undefined symbol detection**

### Emulator
- **16 general-purpose registers** (r0-r15)
- **3 control and status registers** (CSR)
- **Memory-mapped** architecture
- **Interrupt support** (software, timer, terminal)
- **Stack operations** (push/pop)
- Rich instruction set including:
  - Arithmetic operations (add, sub, mul, div)
  - Logical operations (and, or, xor, not)
  - Shift operations (shl, shr)
  - Control flow (call, ret, jmp, branches)
  - Memory operations (ld, st)
  - System operations (halt, int, iret)

## 🏗️ Architecture

The simulated CPU architecture features:

- **Register Set:**
  - `r0-r13`: General-purpose registers
  - `r14` (`sp`): Stack pointer
  - `r15` (`pc`): Program counter
  - `%status`, `%handler`, `%cause`: Control and status registers

- **Memory Model:**
  - 32-bit addressing
  - Little-endian byte order
  - Unordered memory map (no fixed memory regions)

- **Addressing Modes:**
  - Immediate: `$literal`
  - Register direct: `%reg`
  - Register indirect: `[%reg]`
  - Register with offset: `[%reg + offset]`
  - Memory direct: `symbol` or `literal`

## 📁 Project Structure

```
assembler-linker-emulator/
├── inc/                      # Header files
│   ├── assembler.hpp        # Assembler classes and structures
│   ├── linker.hpp           # Linker classes and structures
│   └── emulator.hpp         # Emulator classes and structures
├── src/                      # Implementation files
│   ├── assembler.cpp        # Assembler implementation
│   ├── linker.cpp           # Linker implementation
│   └── emulator.cpp         # Emulator implementation
├── misc/                     # Parser and lexer definitions
│   ├── lexer.l              # Flex lexer specification
│   └── parser.y             # Bison parser specification
├── test/                     # Example assembly programs
│   ├── main.s               # Main program entry point
│   ├── math.s               # Math functions library
│   ├── handler.s            # Interrupt handler dispatcher
│   ├── isr_timer.s          # Timer interrupt service routine
│   ├── isr_terminal.s       # Terminal interrupt service routine
│   └── isr_software.s       # Software interrupt service routine
├── makefile                  # Build configuration
├── start.sh                 # Example build and run script
└── README.md                # This file
```

## 🔧 Prerequisites

- **C++ Compiler** (g++ with C++11 support or later)
- **GNU Make**
- **Flex** (Fast Lexical Analyzer)
- **Bison** (GNU Parser Generator)

## 🔨 Building

To build all components:

```bash
make all
```

To build individual components:

```bash
make assembler    # Build only the assembler
make linker       # Build only the linker
make emulator     # Build only the emulator
```

To clean build artifacts:

```bash
make clean
```

## 🚀 Usage

### Assembler

The assembler translates assembly source files (`.s`) into relocatable object files (`.o`).

```bash
./assembler -o <output.o> <input.s>
```

**Example:**
```bash
./assembler -o main.o test/main.s
./assembler -o math.o test/math.s
```

**Output format:** Object files contain sections, symbol tables, and relocation information.

### Linker

The linker combines multiple object files into a single executable hex file.

```bash
./linker -hex -place=<section>@<address> -o <output.hex> <input1.o> <input2.o> ...
```

**Options:**
- `-hex`: Generate hex output format (required)
- `-place=<section>@<address>`: Place section at specific memory address
- `-o <output.hex>`: Specify output file name

**Example:**
```bash
./linker -hex \
  -place=my_code@0x40000000 \
  -place=math@0xF0000000 \
  -o program.hex \
  handler.o math.o main.o isr_terminal.o isr_timer.o isr_software.o
```

### Emulator

The emulator loads and executes the hex file.

```bash
./emulator <program.hex>
```

**Example:**
```bash
./emulator program.hex
```

The emulator will execute the program until a `halt` instruction is encountered, then print the final processor state showing all register values.

## 📝 Assembly Language Syntax

### Directives

```assembly
.global symbol1, symbol2, ...    # Export symbols
.extern symbol1, symbol2, ...    # Import external symbols
.section section_name            # Define a section
.word value                      # Define 4-byte word (literal or symbol)
.skip n                          # Skip n bytes
.end                             # End of file
```

### Instructions

```assembly
# Arithmetic
add %rs, %rd        # rd = rd + rs
sub %rs, %rd        # rd = rd - rs
mul %rs, %rd        # rd = rd * rs
div %rs, %rd        # rd = rd / rs

# Logical
not %rd             # rd = ~rd
and %rs, %rd        # rd = rd & rs
or %rs, %rd         # rd = rd | rs
xor %rs, %rd        # rd = rd ^ rs

# Shift
shl %rs, %rd        # rd = rd << rs
shr %rs, %rd        # rd = rd >> rs

# Control flow
call operand        # Call subroutine
ret                 # Return from subroutine
jmp operand         # Unconditional jump
beq %r1, %r2, dst   # Branch if equal
bne %r1, %r2, dst   # Branch if not equal
bgt %r1, %r2, dst   # Branch if greater than

# Memory
ld operand, %rd     # Load to register
st %rs, operand     # Store from register
push %rs            # Push register to stack
pop %rd             # Pop from stack to register

# System
halt                # Stop execution
int                 # Software interrupt
iret                # Return from interrupt

# CSR operations
csrrd %csr, %rd     # Read CSR to register
csrwr %rs, %csr     # Write register to CSR

# Other
xchg %r1, %r2       # Exchange register contents
```

### Labels and Comments

```assembly
label_name:         # Define a label
# This is a comment
```

## 💡 Example

A complete example is provided in the `test/` directory and can be run using:

```bash
./start.sh
```

This script:
1. Assembles all test files
2. Links them with specific memory layout
3. Runs the emulator on the resulting program

The example demonstrates:
- Function calls across different sections
- Interrupt handling
- Stack operations
- Arithmetic operations
