# Super Star Trek: Binary Exploitation & Control Flow Patching

A technical demonstration of reverse engineering, dynamic analysis, and binary instrumentation using a legacy C implementation of the strategy game *Super Star Trek*. 

## 🎯 Overview
In this scenario, a sentient AI has seized control of the Starship Enterprise. To prevent a catastrophic failure, the "Self-Destruct" authentication sequence must be bypassed. This project involves identifying runtime-generated security keys and patching compiled machine code to alter the program's logical execution flow.

## 🛠️ Tools Used
* [cite_start]**Ghidra:** Disassembled and decompiled the ELF/Mach-O binaries to map control flow[cite: 1, 4].
* [cite_start]**GDB:** Performed dynamic analysis to track register values and memory states during execution.
* [cite_start]**Hex Editor:** Manually modified instruction opcodes and data section strings.

## 🔍 Technical Analysis & Discovery

### 1. Runtime Password Extraction
[cite_start]Initial static analysis of the `dsetrct` (Destruct) function pointed to an empty variable `citem`, suggesting the password was not stored in plaintext[cite: 181]. [cite_start]Further investigation of cross-references for the `ffzagg` variable led to the `choose` function[cite: 181].

* [cite_start]**Observation:** The function assigned a hex value: `0x7270706865637267`[cite: 181].
* [cite_start]**Decoding:** By reversing the little-endian bytes (`67 72 63 65 68 70 70 72`), the ASCII password was revealed as `grcehppr`[cite: 181].

### 2. Control Flow Patching
[cite_start]The authentication logic relied on a `JNZ` (Jump if Not Zero) instruction at offset `00402b5d` to redirect the user to a "PASSWORD REJECTED" state[cite: 181].

* [cite_start]**The Patch:** To bypass this, the 6-byte instruction (`0f 85 ab 00 00 00`) was neutralized[cite: 181].
* [cite_start]**Implementation:** A **NOP Sled** (`90 90 90 90 90 90`) was applied[cite: 181]. [cite_start]This forces the CPU to "slide" through the check and land directly in the "PASSWORD ACCEPTED" routine regardless of the user's input[cite: 181].

### 3. Binary Instrumentation
[cite_start]In addition to logic bypassing, the binary's data section was modified at offset `0041e154` to update the software credits while maintaining strict memory alignment[cite: 181].

## 🚀 Repository Structure
* [cite_start]**/binaries**: Contains the original AArch64 baseline and the patched x86_64 ELF executable.
* [cite_start]**/docs**: Detailed technical report and assembly-level walkthroughs.
* **README.md**: High-level project summary and proof of concept.

## 🖼️ Proof of Concept
![Terminal Proof](./docs/screenshot.png)

---
*Disclaimer: This project was conducted in a controlled environment for educational purposes in cybersecurity and software instrumentation.*
