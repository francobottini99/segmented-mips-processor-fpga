# Design and Implementation of a Segmented MIPS Processor on an FPGA

### Authors:
- **Bottini, Franco Nicolas**
- **Robledo, Valentin**

## Table of Contents

- [How to use this repository?](#how-to-use-this-repository)
   - [1. Clone the repository](#1-clone-the-repository)
   - [2. Create a new project in Vivado](#2-create-a-new-project-in-vivado)
   - [3. Add project files](#3-add-the-project-files)
   - [4. Generate the bitstream](#4-generate-the-bitstream)
   - [5. Program the FPGA](#5-program-the-fpga)
   - [6. Run the user application](#6-run-the-user-application)
- [How to use the user application?](#how-to-use-the-user-application)
   - [1. Load a program into instruction memory](#1-load-a-program-into-instruction-memory)
   - [2. Run the loaded program in instruction memory](#2-run-the-loaded-program-in-instruction-memory)
   - [3. Run the loaded program in instruction memory step-by-step](#3-run-the-loaded-program-in-instruction-memory-step-by-step)
   - [4. Run the loaded program in instruction memory in debug mode](#4-run-the-loaded-program-in-instruction-memory-in-debug-mode)
   - [5. Delete the loaded program in instruction memory](#5-delete-the-loaded-program-in-instruction-memory)
   - [6. Exit the application](#6-exit-the-application)
- [1. Summary](#1-summary)
- [2. Processor Specifications](#2-processor-specifications)
- [3. Development](#3-development)
    - [3.1. Processor Architecture](#31-processor-architecture)
    - [3.2. Pipeline Stages](#32-pipeline-stages)
        - [3.2.1. Stage `IF`](#321-stage-if)
        - [3.2.2. Stage `ID`](#322-stage-id)
        - [3.2.3. Stage `EX`](#323-stage-ex)
        - [3.2.4. Stage `MEM`](#324-stage-mem)
        - [3.2.5. Stage `WB`](#325-stage-wb)
    - [3.3. Control and Hazard Detection](#33-control-and-hazard-detection)
        - [3.3.1. Main Control Unit](#331-main-control-unit)
            - [3.3.1.1. Control Table](#3311-control-table)
        - [3.3.2. ALU Control Unit](#332-alu-control-unit)
            - [3.3.2.1. Control Table](#3321-control-table)
        - [3.3.3. Short Circuit Unit](#333-short-circuit-unit)
            - [3.3.3.1. Control Table](#3331-control-table)
        - [3.3.4. Hazard Detection Unit](#334-hazard-detection-unit)
            - [3.3.4.1 Hazard Detection Control Table](#3341-control-table)
    - [3.4. Processor Operation](#34-processor-operation)
        - [3.4.1. UART](#341-uart)
        - [3.4.2. Debugger](#342-debugger)
- [4. Simulations](#4-simulations)
  	- [4.1. `IF` Stage](#41-if-stage-tests)
		- [4.1.1. Test Bench for the `PC` Module](#411-test-bench-for-the-pc-module)
		- [4.1.2. Test Bench for the `Instruction Memory` Module](#412-test-bench-for-the-instruction-memory-module)
		- [4.1.3. Integration Test Bench](#413-integration-test-bench)
  	- [4.2. `ID` Stage](#42-id-stage-tests)
		- [4.2.1. Test Bench for the `Register Bank` Module](#421-test-bench-for-the-register-bank-module)
		- [4.2.2. Test Bench for the `Main Control` Module](#422-test-bench-for-the-main-control-module)
		- [4.2.3. Integration Test Bench](#423-integration-test-bench)
  	- [4.3. `EX` Stage](#43-ex-stage-tests)
		- [4.3.1. Test Bench for the `ALU` Module](#431-test-bench-for-the-alu-module)
		- [4.3.2. Test Bench for the `ALU Control` Module](#432-test-bench-for-the-alu-control-module)
		- [4.3.3. Integration Test Bench](#433-integration-test-bench)
  	- [4.4. `MEM` Stage](#44-mem-stage-tests)
		- [4.4.1. Test Bench for the `Data Memory` Module](#441-test-bench-for-the-data-memory-module)
		- [4.4.2. Integration Test Bench](#442-integration-test-bench)
  	- [4.5. `WB` Stage](#45-wb-stage-tests)
		- [4.5.1. Integration Test Bench](#451-integration-test-bench)
	- [4.6. `Hazards Units` Tests](#46-hazards-units-tests)
		- [4.6.1. Test Bench for the `Short Circuit` Module](#461-test-bench-for-the-short-circuit-module)
		- [4.6.2. Test Bench for the `Risk Detection` Module](#462-test-bench-for-the-risk-detection-module)
	- [4.7. Other Tests](#47-other-tests)
	- [4.8. MIPS Integration Test](#48-mips-integration-test)
		- [4.8.1. Annex 1: Test Programs](#481-annex-1-test-programs) 
			- [4.8.1.1. Program 1](#4811-program-1)
			- [4.8.1.2. Program 2](#4812-program-2)
			- [4.8.1.3. Program 3](#4813-program-3)
		- [4.8.2. Annex 2: Execution Results](#482-annex-2-execution-results)
			- [4.8.2.1. Program 1 Execution](#4821-program-1-execution)
			- [4.8.2.2. Program 2 Execution](#4822-program-2-execution)
			- [4.8.2.3. Program 3 Execution](#4823-program-3-execution)
	- [4.9. System Test](#49-system-test)
- [5. Results](#5-results)
   - [5.1. Program 1](#51-program-1)
   - [5.2. Program 2](#52-program-2)
   - [5.3. Program 3](#53-program-3)
- [6. References](#6-references)
  
## How to use this repository?

### 1. Clone the repository
The repository can be cloned using the `git clone` command:

```bash
git clone https://github.com/francobottini99/MIPSFPGA-2023.git
```

### 2. Create a new project in Vivado
To create a new project in Vivado, open the software and select the `Create Project` option in the start window. Then, enter a name for the project and select the location where it will be saved. Next, select the `RTL Project` option and the `Do not specify sources at this time` option. Finally, select the `Basys 3` development board and `Verilog` as the hardware description language. This will create a new empty project.

> [!WARNING]
> It is important to select the option `Do not specify sources at this time`.

### 3. Add the project files
To add the project files, right-click on the `Sources` folder in the project and select the `Add Sources` option. Then, select the `Add or create design sources` option and the `Add Directories` option. Finally, select the `vivado.src/sources` directory from the cloned repository. Repeat this process for the `vivado.src/constrs` directory and the `vivado.src/sim` directory.

### 4. Generate the bitstream
Generate the bitstream of the project using the `Generate Bitstream` option from the `Flow Navigator` menu.

### 5. Program the FPGA
Connect the development board to the computer and program the FPGA using the `Program Device` option from the `Flow Navigator` menu.

### 6. Run the user application
To operate the processor, use the `python.src/app.py` application. To run the application, open a terminal in the `python.src` directory and execute the command `py app.py`.

## How to use the user application?

The user application runs in a terminal and allows interaction with the processor. To run the application, open a terminal in the `python.src` directory and execute the command `py Api.py`. The first window displayed is as follows:

<p align="center">
  <img src="imgs/Api_Port_Select.png" alt="MIPS Segmented Processor">
</p>

In this window, you must select the serial port to which the development board is connected. If the connection is established correctly, the following window will appear:

<p align="center">
  <img src="imgs/Api_Baudrate_Select.png" alt="MIPS Segmented Processor">
</p>

This window allows you to select the data transmission speed. You must select the same speed used to program the FPGA. If the speed is selected correctly, the main menu of the application will appear:

<p align="center">
  <img src="imgs/Api_Main_Menu.png" alt="MIPS Segmented Processor">
</p>

This menu allows you to choose from the following options:

   - `1`: Load a program into the instruction memory.
   - `2`: Run the program loaded in the instruction memory.
   - `3`: Run the program loaded in the instruction memory step by step.
   - `4`: Run the program loaded in the instruction memory in debug mode.
   - `5`: Delete the program loaded in the instruction memory.
   - `6`: Exit the application.

> [!NOTE]
> By default, the development board connects to a serial port with a data transmission speed of 19200 baud.

### 1. Load a program into instruction memory

<p align="center">
  <img src="imgs/Api_Path_Input.png" alt="Load Program">
</p>

This option allows you to load a program into the instruction memory. To do so, you must enter the name of the file containing the program. The program must be written in MIPS assembly language. The selected program is compiled and loaded into the instruction memory. If the program loads correctly, the compilation result and a success message will be displayed:

<p align="center">
  <img src="imgs/Api_Load_Result.png" alt="Load Result">
</p>

### 2. Run the loaded program in instruction memory

This option allows you to run the program loaded in the instruction memory. If the program runs successfully, the final state of the registers and data memory will be displayed:

<p align="center">
  <img src="imgs/Api_Execute_Program_2.png" alt="Complete Execution">
</p>

### 3. Run the loaded program in instruction memory step-by-step

This option allows you to run the program loaded in the instruction memory step by step. In each step, the state of the registers and data memory is displayed:

<p align="center">
  <img src="imgs/Api_Execution_Step.png" alt="Step by Step">
</p>

You can move to the next step by entering the letter `N` and pressing `Enter`. To exit step-by-step mode, enter the letter `S` and press `Enter`.

### 4. Run the loaded program in instruction memory in debug mode

This option allows you to run the program loaded in the instruction memory in debug mode. In this mode, the evolution of the state of the registers and memory is shown throughout the full execution of the program:

<p align="center">
  <img src="imgs/Api_Execute_Debug.png" alt="Debug Mode">
</p>

### 5. Delete the loaded program in instruction memory

This option allows you to delete the program loaded in the instruction memory. If the program is deleted successfully, a success message will be displayed:

<p align="center">
  <img src="imgs/Api_Delete_Program.png" alt="Delete Program">
</p>

### 6. Exit the application

This option allows you to exit the application. You can also exit the application from any menu using the key combination `Ctrl + C`.

## 1. Summary
This work was developed as part of the Computer Architecture course in the Computer Engineering program at the Faculty of Exact, Physical, and Natural Sciences of the National University of Córdoba. It involves the implementation of a simplified version of a segmented **MIPS** processor on a **FPGA** development board.

A [MIPS (*Microprocessor without Interlocked Pipeline Stages*)](https://en.wikipedia.org/wiki/MIPS_(processor)) processor is a type of 32-bit microprocessor that uses a reduced instruction set computer (RISC) architecture. This architecture is characterized by its simplicity, using a small number of fixed-size instructions that execute in a single clock cycle.

The segmented design, also known as pipelined design or ["*pipeline*"](https://en.wikipedia.org/wiki/Pipeline_(electronics)), is a technique that allows multiple instructions to be executed simultaneously in different processing stages. This increases the efficiency and performance of the processor.

In this project, the **MIPS** processor will be implemented on an [FPGA (Field Programmable Gate Array)](https://en.wikipedia.org/wiki/Field-programmable_gate_array), a semiconductor device that can be programmed to perform a wide variety of digital processing tasks. The simplified version of the **MIPS** processor was designed to facilitate its understanding and implementation on the **FPGA**.

> [!NOTE]
> The project was developed in the hardware description language [Verilog](https://en.wikipedia.org/wiki/Verilog) using [Vivado](https://www.xilinx.com/products/design-tools/vivado.html) software from [Xilinx](https://www.xilinx.com/). The **MIPS** processor was implemented on the **FPGA** development board [Basys 3](https://digilent.com/reference/programmable-logic/basys-3/start) from [Digilent](https://digilent.com/).

## 2. Processor Specifications
The **MIPS** processor implements a 5-stage pipeline, with each stage executing in one clock cycle. The stages are as follows:

1. **IF (Instruction Fetch)**: The instruction is fetched from the instruction memory.
2. **ID (Instruction Decode)**: The instruction is decoded, and the registers are read.
3. **EX (Execute)**: The instruction is executed.
4. **MEM (Memory Access)**: Data is accessed from memory.
5. **WB (Write Back)**: The results are written back to the registers.

The execution pipeline supports the following subset of **MIPS IV** instructions:

| Instruction | Description | Format | Result | Type |
| ----------- | ----------- | ------- | ------ | ----- |
| `sll` | Shift left logical | `sll $rd, $rt, shamt` | `$rd = $rt << shamt` | R |
| `srl` | Shift right logical | `srl $rd, $rt, shamt` | `$rd = $rt >> shamt` | R |
| `sra` | Shift right arithmetic | `sra $rd, $rt, shamt` | `$rd = $rt >> shamt` | R |
| `sllv` | Shift left logical variable | `sllv $rd, $rt, $rs` | `$rd = $rt << $rs` | R |
| `srlv` | Shift right logical variable | `srlv $rd, $rt, $rs` | `$rd = $rt >> $rs` | R |
| `srav` | Shift right arithmetic variable | `srav $rd, $rt, $rs` | `$rd = $rt >> $rs` | R |
| `add` | Suma | `add $rd, $rs, $rt` | `$rd = $rs + $rt` | R |
| `addu` | Suma sin signo | `addu $rd, $rs, $rt` | `$rd = $rs + $rt` | R |
| `sub` | Resta | `sub $rd, $rs, $rt` | `$rd = $rs - $rt` | R |
| `subu` | Resta sin signo | `subu $rd, $rs, $rt` | `$rd = $rs - $rt` | R |
| `and` | AND lógico | `and $rd, $rs, $rt` | `$rd = $rs & $rt` | R |
| `or` | OR lógico | `or $rd, $rs, $rt` | `$rd = $rs \| $rt` | R |
| `xor` | XOR lógico | `xor $rd, $rs, $rt` | `$rd = $rs ^ $rt` | R |
| `nor` | NOR lógico | `nor $rd, $rs, $rt` | `$rd = ~($rs \| $rt)` | R |
| `slt` | Set on less than | `slt $rd, $rs, $rt` | `$rd = ($rs < $rt) ? 1 : 0` | R |
| `lb` | Load byte | `lb $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `lh` | Load halfword | `lh $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `lw` | Load word | `lw $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `lwu` | Load word unsigned | `lwu $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `lbu` | Load byte unsigned | `lbu $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `lhu` | Load halfword unsigned | `lhu $rt, imm($rs)` | `$rt = MEM[$rs + imm]` | I |
| `sb` | Store byte | `sb $rt, imm($rs)` | `MEM[$rs + imm] = $rt` | I |
| `sh` | Store halfword | `sh $rt, imm($rs)` | `MEM[$rs + imm] = $rt` | I |
| `sw` | Store word | `sw $rt, imm($rs)` | `MEM[$rs + imm] = $rt` | I |
| `addi` | Suma inmediata | `addi $rt, $rs, imm` | `$rt = $rs + imm` | I |
| `andi` | AND lógico inmediato | `andi $rt, $rs, imm` | `$rt = $rs & imm` | I |
| `ori` | OR lógico inmediato | `ori $rt, $rs, imm` | `$rt = $rs \| imm` | I |
| `xori` | XOR lógico inmediato | `xori $rt, $rs, imm` | `$rt = $rs ^ imm` | I |
| `lui` | Load upper immediate | `lui $rt, imm` | `$rt = imm << 16` | I |
| `slti` | Set on less than inmediato | `slti $rt, $rs, imm` | `$rt = ($rs < imm) ? 1 : 0` | I |
| `beq` | Branch on equal | `beq $rs, $rt, imm` | `if ($rs == $rt) PC = PC + 4 + imm << 2` | I |
| `bne` | Branch on not equal | `bne $rs, $rt, imm` | `if ($rs != $rt) PC = PC + 4 + imm << 2` | I |
| `j` | Jump | `j target` | `PC = target << 2` | I |
| `jal` | Jump and link | `jal target` | `$ra = PC + 4; PC = target << 2` | I |
| `jr` | Jump register | `jr $rs` | `PC = $rs` | J |
| `jalr` | Jump and link register | `jalr $rs` | `$ra = PC + 4; PC = $rs` | J |

Additionally, the implemented processor detects and eliminates structural, data, and control hazards. These are:

- **Structural hazards**: Occur when two instructions attempt to access the same resource simultaneously.
- **Data hazards**: Occur when one instruction depends on the result of another instruction that has not yet completed.
- **Control hazards**: Occur when a conditional jump instruction alters the program's execution flow.

To resolve these hazards, the following techniques were implemented:

- **Forwarding**: Used to resolve data hazards. It involves forwarding the result of an instruction to an earlier pipeline stage so that another instruction can use it.
- **Stalling**: Used to resolve data and control hazards. It involves halting the progress of the pipeline until the hazard is resolved.

## 3. Development

### 3.1. Processor Architecture
The architecture of the segmented **MIPS** processor is shown in the following figure:

<p align="center">
  <img src="imgs/MIPS_Diagram.png" alt="Segmented MIPS Processor">
</p>

### 3.2. Pipeline Stages
The *pipeline* is implemented through the modules `if_id`, `id_ex`, `ex_mem`, and `mem_wb`, which are responsible for interconnecting the various processing stages. These stages are:

#### 3.2.1. Stage `IF`

<p align="center">
  <img src="imgs/Etapa_IF.png" alt="Instruction Fetch">
</p>

The **IF** (*Instruction Fetch*) stage is the first stage in the pipeline of a MIPS processor. In this stage, the instruction to be executed is fetched from the instruction memory.

The `_if` module in the Verilog code represents this stage. This module has several inputs and outputs that allow it to interact with other pipeline stages and the instruction memory.

The module inputs include control signals such as `i_clk` (the clock signal), `i_reset` (to reset the module), `i_halt` (to halt execution), `i_not_load` (to indicate whether an instruction should be loaded), `i_enable` (to enable the module), and `i_next_pc_src` (to select the source of the next program counter or PC). It also includes inputs for the instruction to be written to memory (`i_instruction`), and the values of the next sequential PC (`i_next_seq_pc`) and non-sequential PC (`i_next_not_seq_pc`).

The module outputs include signals indicating whether the instruction memory is full (`o_full_mem`) or empty (`o_empty_mem`), the instruction fetched from memory (`o_instruction`), and the value of the next sequential PC (`o_next_seq_pc`).

Inside the module, various components are used to perform the necessary operations. These include a multiplexer (`mux_2_unit_pc`) to select the next PC, an adder (`adder_unit`) to calculate the next sequential PC, a PC module (`pc_unit`) to maintain and update the PC value, and an instruction memory unit (`instruction_memory_unit`) to store and retrieve the instructions to be executed.

#### 3.2.2. Stage `ID`

<p align="center">
  <img src="imgs/Etapa_ID.png" alt="Instruction Decode">
</p>

The **ID** (*Instruction Decode*) stage is the second stage in the pipeline. In this stage, the instruction fetched from the instruction memory is decoded, and the necessary register values are read for execution.

The `_id` module in the Verilog code represents this stage. This module has several inputs and outputs that allow it to interact with other pipeline stages and the register bank.

The module inputs include control signals such as `i_clk` (the clock signal), `i_reset` (to reset the module), `i_flush` (to flush the register bank), `i_reg_write_enable` (to enable writing to the register bank), and `i_ctr_reg_src` (to select the control register source). It also includes inputs for the instruction to decode (`i_instruction`), the values of the EX stage A and B buses (`i_ex_bus_a` and `i_ex_bus_b`), and the value of the next sequential PC (`i_next_seq_pc`).

The module outputs include control signals that determine the behavior of the subsequent pipeline stages, such as `o_next_pc_src`, `o_mem_rd_src`, `o_mem_wr_src`, `o_mem_write`, `o_wb`, `o_mem_to_reg`, `o_reg_dst`, `o_alu_src_a`, `o_alu_src_b`, and `o_alu_op`. It also includes outputs for the decoded values of various instruction fields, such as `o_rs`, `o_rt`, `o_rd`, `o_funct`, `o_op`, `o_shamt_ext_unsigned`, `o_inm_ext_signed`, `o_inm_upp`, `o_inm_ext_unsigned`, and the A and B buses values for the EX stage (`o_bus_a` and `o_bus_b`).

Inside the module, various components are used to perform the necessary operations. These include a register bank to store the values of the registers, and several multiplexers and decoders to decode the instruction and generate the control signals for subsequent pipeline stages.

#### 3.2.3. Stage `EX`

<p align="center">
  <img src="imgs/Etapa_EX.png" alt="Execution">
</p>

The **EX** (*Execution*) stage is the third stage in the MIPS pipeline. In this stage, the arithmetic and logical operations specified by the decoded instruction are performed.

The `_ex` module in the Verilog code represents this stage. This module has several inputs and outputs that allow it to interact with other pipeline stages.

The module inputs include control signals such as `i_alu_src_a`, `i_alu_src_b`, `i_reg_dst`, `i_alu_op`, `i_sc_src_a`, `i_sc_src_b`, `i_rt`, `i_rd`, `i_funct`, and data buses like `i_sc_alu_result`, `i_sc_wb_result`, `i_bus_a`, `i_bus_b`, `i_shamt_ext_unsigned`, `i_inm_ext_signed`, `i_inm_upp`, `i_inm_ext_unsigned`, `i_next_seq_pc`.

The module outputs include `o_wb_addr`, `o_alu_result`, `o_sc_bus_b`, `o_sc_bus_a`.

Inside the module, various components are used to perform the necessary operations. These include an ALU unit (`alu_unit`) to perform arithmetic and logical operations, an ALU control unit (`alu_control_unit`) to generate the control signals for the ALU, and several multiplexers (`mux_alu_src_data_a_unit`, `mux_alu_src_data_b_unit`, `mux_sc_src_a_unit`, `mux_sc_src_b_unit`, `mux_reg_dst_unit`) to select the input data for the ALU and the destination register address for the WB stage.

#### 3.2.4. Stage `MEM`

<p align="center">
  <img src="imgs/Etapa_MEM.png" alt="Memory">
</p>

The **MEM** (*Memory*) stage is the fourth stage in the MIPS pipeline. In this stage, if the instruction is a load or store operation, data is accessed from the data memory.

The `mem` module in the Verilog code represents this stage. This module has several inputs and outputs that allow it to interact with other pipeline stages and the data memory.

The module inputs include control signals such as `i_clk` (the clock signal), `i_reset` (to reset the module), `i_flush` (to flush the data memory), `i_mem_wr_rd` (to indicate whether to perform a write or read operation on memory), `i_mem_wr_src` (to select the data source for memory write), `i_mem_rd_src` (to select how to read data from memory), and `i_mem_addr` (the address to access in memory). It also includes an input for the data to write to memory (`i_bus_b`).

The module outputs include `o_mem_rd` (the data read from memory) and `o_bus_debug` (a debug bus containing all the data from memory).

Inside the module, various components are used to perform the necessary operations. These include an instance of a `data_memory` module to represent the data memory, several multiplexers (`mux_in_mem_unit`, `mux_out_mem_unit`) to select the data to write to memory and how to read data from memory, and instances of `unsig_extend` and `sig_extend` modules to extend the data read from memory to the correct length, either with sign extension or without it.

#### 3.2.5. Stage `WB`

<p align="center">
  <img src="imgs/Etapa_WB.png" alt="Write Back">
</p>

The **WB** (*Write Back*) stage is the fifth and final stage in the pipeline of the processor. In this stage, the results of the instruction execution are written back to the registers.

The `wb` module in the Verilog code represents this stage. This module has several inputs and one output that allow it to interact with other pipeline stages.

The module inputs include `i_mem_to_reg` (a control signal indicating whether the result from the memory operation should be written to the registers), `i_alu_result` (the result from the ALU operation), and `i_mem_result` (the result from the memory operation).

The output of the module is `o_wb_data` (the data to be written to the registers).

Inside the module, a multiplexer (`mux_wb_data`) is used to select between `i_alu_result` and `i_mem_result` based on the `i_mem_to_reg` signal. The result of this selection is sent to the output `o_wb_data`.

### 3.3. Control and Hazard Detection
Control and hazard detection in the processor are implemented through the following modules:

#### 3.3.1. Main Control Unit

<p align="center">
  <img src="imgs/Main_Control.png" alt="Main Control">
</p>

The main control unit is responsible for generating the control signals for various operations in a processor. The control unit takes several fields of the current instruction as input, such as the operation code (`i_op`), the function code (`i_funct`), and additional signals like `i_bus_a_not_equal_bus_b` and `i_instruction_is_nop`. Based on these inputs, the control unit determines the values of the control signals that are used to control different functional units of the processor.

- `next_pc_src`: Controls the source of the next program counter (PC) value.
- `jmp_ctrl`: Controls the type of jump (whether it's a jump and its direction).
- `reg_dst`: Selects the destination of the ALU result to write to the register.
- `alu_src_a` and `alu_src_b`: Select the input sources for the ALU.
- `code_alu_op`: Specifies the ALU operation.
- `mem_rd_src`: Selects the source for memory read data.
- `mem_wr_src`: Selects the source for memory write data.
- `mem_write`: Controls whether a memory write operation occurs.
- `wb`: Controls whether the register should be updated with a new value.
- `mem_to_reg`: Controls whether the result should be written to the register from memory.

All of these signals are concentrated in the `o_ctrl_bus`, which is used to control the various functional units of the processor.

##### 3.3.1.1. Control Table
The truth table for the main control unit is shown below:


| i_op             | i_funct         | i_bus_a_not_equal_bus_b | i_instruction_is_nop | next_pc_src | jmp_ctrl | reg_dst | alu_src_a          | alu_src_b          | code_alu_op | mem_rd_src         | mem_wr_src         | mem_write | wb   | mem_to_reg         |
|------------------|-----------------|-------------------------|-----------------------|-------------|----------|---------|--------------------|--------------------|-------------|---------------------|---------------------|-----------|------|--------------------|
| `******` | `******` | `*` | `1` | `1` | `xx` | `xx` | `x` | `xxx` | `xxx` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_R_TYPE` | `CODE_FUNCT_JR` | `*` | `0` | `1` | `01` | `xx` | `x` | `xxx` | `110` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_R_TYPE` | `CODE_FUNCT_JALR` | `*` | `0` | `1` | `01` | `10` | `1` | `000` | `110` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_R_TYPE` | `CODE_FUNCT_SLL` | `*` | `0` | `1` | `xx` | `01` | `0` | `100` | `110` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_R_TYPE` | `CODE_FUNCT_SRL` | `*` | `0` | `1` | `xx` | `01` | `0` | `100` | `110` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_R_TYPE` | `CODE_FUNCT_SRA` | `*` | `0` | `1` | `xx` | `01` | `0` | `100` | `110` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_R_TYPE` | `******` | `*` | `0` | `1` | `xx` | `01` | `1` | `100` | `110` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_LW` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `000` | `xx` | `0` | `1` | `0` |
| `CODE_OP_SW` | `******` | `*` | `0` | `1` | `xx` | `xx` | `1` | `010` | `000` | `xxx` | `00` | `1` | `0` | `x` |
| `CODE_OP_BEQ` | `******` | `1` | `0` | `1` | `xx` | `xx` | `x` | `xxx` | `001` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_BEQ` | `******` | `0` | `0` | `1` | `00` | `xx` | `x` | `xxx` | `001` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_BNE` | `******` | `1` | `0` | `1` | `00` | `xx` | `x` | `xxx` | `001` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_BNE` | `******` | `0` | `0` | `1` | `xx` | `xx` | `x` | `xxx` | `001` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_ADDI` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_J` | `******` | `*` | `0` | `1` | `10` | `xx` | `x` | `xxx` | `111` | `xxx` | `xx` | `0` | `0` | `x` |
| `CODE_OP_JAL` | `******` | `*` | `0` | `1` | `10` | `10` | `x` | `000` | `111` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_ANDI` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `011` | `010` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_ORI` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `011` | `011` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_XORI` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `011` | `100` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_SLTI` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `101` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_LUI` | `******` | `*` | `0` | `1` | `xx` | `00` | `x` | `001` | `000` | `xxx` | `xx` | `0` | `1` | `1` |
| `CODE_OP_LBU` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `010` | `xx` | `0` | `1` | `0` |
| `CODE_OP_LBU` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `100` | `xx` | `0` | `1` | `0` |
| `CODE_OP_LH` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `001` | `xx` | `0` | `1` | `0` |
| `CODE_OP_LHU` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `011` | `xx` | `0` | `1` | `0` |
| `CODE_OP_LWU` | `******` | `*` | `0` | `1` | `xx` | `00` | `1` | `010` | `000` | `000` | `xx` | `0` | `1` | `0` |
| `CODE_OP_SB` | `******` | `*` | `0` | `1` | `xx` | `xx` | `1` | `010` | `000` | `xxx` | `10` | `1` | `0` | `x` |
| `CODE_OP_SH` | `******` | `*` | `0` | `1` | `xx` | `xx` | `1` | `010` | `000` | `xxx` | `01` | `1` | `0` | `x` |

> [!NOTE]
> The fields marked with '*' indicate that those bits can have any value and do not affect the final result. The fields marked with 'x' indicate an indeterminate state as the signal is not used in that instruction.

#### 3.3.2. ALU Control Unit

<p align="center">
  <img src="imgs/Alu_Control.png" alt="ALU Control">
</p>

The `alu_control` module in the Verilog code represents the ALU (Arithmetic Logic Unit) control unit. This unit generates the control signals for the ALU based on the operation and function codes of the instruction.

The inputs to the module are `i_funct` (the funct field of the instruction, which specifies the operation to perform in the case of R-type instructions) and `i_alu_op` (the ALU operation code, which specifies the operation to perform). The latter comes from the main control unit.

The output of the module is `o_alu_ctr` (the control signal for the ALU, which indicates the operation to be performed).

##### 3.3.2.1. Control Table
The truth table for the ALU control unit is shown below:

| i_alu_op        | i_funct          | o_alu_ctr | operation |
|-----------------|------------------|-----------|-----------|
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SLL` | `0000` | `b << a` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SRL` | `0001` | `b >> a` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SRA` | `0010` | `$signed(b) >>> a` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_ADD` | `0011` | `$signed(a) + $signed(b)` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_ADDU` | `0100` | `a + b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SUB` | `0101` | `$signed(a) - $signed(b)` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SUBU` | `0110` | `a - b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_AND` | `0111` | `a & b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_OR` | `1000` | `a \| b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_XOR` | `1001` | `a ^ b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_NOR` | `1010` | `~(a \| b)` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SLT` | `1011` | `$signed(a) < $signed(b)` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SLLV` | `1100` | `a << b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SRLV` | `1101` | `a >> b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_SRAV` | `1110` | `$signed(a) >>> b` |
| `CODE_ALU_CTR_R_TYPE` | `CODE_FUNCT_JALR` | `1111` | `b` |
| `CODE_ALU_CTR_LOAD_TYPE` | `******` | `0011` | `$signed(a) + $signed(b)` |
| `CODE_ALU_CTR_STORE_TYPE` | `******` | `0011` | `$signed(a) + $signed(b)` |
| `CODE_ALU_CTR_JUMP_TYPE` | `******` | `1111` | `b` |
| `CODE_ALU_CTR_ADDI` | `******` | `0011` | `$signed(a) + $signed(b)` |
| `CODE_ALU_CTR_ANDI` | `******` | `0111` | `a & b` |
| `CODE_ALU_CTR_ORI` | `******` | `1000` | `a \| b` |
| `CODE_ALU_CTR_XORI` | `******` | `1001` | `a ^ b` |
| `CODE_ALU_CTR_SLTI` | `******` | `1011` | `$signed(a) < $signed(b)` |

> [!NOTE]
> The fields marked with '*' indicate that those bits can have any value and do not affect the final result. The fields marked with 'x' indicate an indeterminate state as the signal is not used in that instruction.

#### 3.3.3. Short Circuit Unit

<p align="center">
  <img src="imgs/SC_Unit.png" alt="Short Circuit Unit">
</p>

The `short_circuit` module in the Verilog code represents a short circuit unit. This unit is used to implement data forwarding, a technique that helps minimize data hazards in the processor's pipeline.

The inputs to the module are:

- `i_ex_mem_wb` and `i_mem_wb_wb`: These signals indicate whether there are valid data in the `EX/MEM` and `MEM/WB` stages of the pipeline, respectively.

- `i_id_ex_rs` and `i_id_ex_rt`: These are the source registers from the `ID/EX` stage of the pipeline.

- `i_ex_mem_addr` and `i_mem_wb_addr`: These are the memory addresses from the `EX/MEM` and `MEM/WB` stages of the pipeline, respectively.

The outputs of the module are:

- `o_sc_data_a_src` and `o_sc_data_b_src`: These signals indicate the data source for the source registers `rs` and `rt`, respectively.

The module logic checks if the memory addresses from the `EX/MEM` and `MEM/WB` stages match the source registers from the `ID/EX` stage. If so, and if valid data exists in the `EX/MEM` and `MEM/WB` stages, the data is forwarded from these stages. If not, the data comes from the `ID/EX` stage.

This helps minimize data hazards by allowing instructions to use the results of previous instructions as soon as they are available, rather than waiting for the previous instructions to complete and for the results to be written back to the registers.

##### 3.3.3.1 Control Table
The truth table for the short circuit unit is shown below:

| i_ex_mem_wb | i_mem_wb_wb | i_ex_mem_addr == i_id_ex_rs | i_mem_wb_addr == i_id_ex_rs | o_sc_data_a_src |
|-------------|-------------|-----------------------------|-----------------------------|-----------------|
| `1`           | `-`           | `1`                           | `-`                           | `EX_MEM`          |
| `0 or -`      | `1`           | `-`                           | `1`                           | `MEM_WB`          |
| `0 or -`      | `0 or -`      | `0 or -`                      | `0 or -`                      | `ID_EX`          |

| i_ex_mem_wb | i_mem_wb_wb | i_ex_mem_addr == i_id_ex_rt | i_mem_wb_addr == i_id_ex_rt | o_sc_data_b_src |
|-------------|-------------|-----------------------------|-----------------------------|-----------------|
| `1`           | `-`           | `1`                           | `-`                           | `EX_MEM`         |
| `0 or -`      | `1`           | `-`                           | `1`                           | `MEM_WB`          |
| `0 or -`      | `0 or -`      | `0 or -`                      | `0 or -`                      | `ID_EX`           |

> [!NOTE]
> In this table, `EX_MEM`, `MEM_WB`, and `ID_EX` represent the pipeline stages from which the data is obtained. `i_ex_mem_addr == i_id_ex_rs` and `i_ex_mem_addr == i_id_ex_rt` represent equality comparisons between memory addresses and source registers. If the comparison is true, it means that the data for the corresponding source register can be obtained from the `EX_MEM` stage. Similarly, `i_mem_wb_addr == i_id_ex_rs` and `i_mem_wb_addr == i_id_ex_rt` represent equality comparisons for the `MEM_WB` stage.

#### 3.3.4. Hazard Detection Unit

<p align="center">
  <img src="imgs/Risk_Detection_Unit.png" alt="Risk Detection Unit">
</p>

The `risk_detection` module in the Verilog code represents a hazard detection unit. This unit is used to detect and handle situations that could cause problems in the processor's pipeline, such as data and control hazards.

The inputs to the module are:

- `i_jmp_stop`, `i_if_id_rs`, `i_if_id_rd`, `i_if_id_op`, `i_if_id_funct`, `i_id_ex_rt`, `i_id_ex_op`: These signals represent various parts of the instructions in the `IF/ID` and `ID/EX` stages of the pipeline, including the operation and function codes, the source and destination registers, and a signal indicating if the jump should be stopped.

The outputs of the module are:

- `o_jmp_stop`: This signal is activated if the processor should stop for one cycle due to a jump, which happens if the instruction in the `IF/ID` stage is a jump and the `i_jmp_stop` signal is not active.
- `o_halt`: This signal is activated if the instruction in the `IF/ID` stage is a program termination instruction.
- `o_not_load`: This signal is activated if the instruction in the `ID/EX` stage is a load instruction and the destination register matches one of the source registers of the instruction in the `IF/ID` stage, or if the `o_jmp_stop` signal is active. This indicates a data hazard.
- `o_ctr_reg_src`: This signal is equal to the `o_not_load` signal and is used to indicate whether the control signals propagate to the next stages or not.

The module logic checks the instructions in the `IF/ID` and `ID/EX` stages of the pipeline and activates the corresponding output signals if it detects a hazard or if a jump instruction uses processor registers (potential hazard).

##### 3.3.4.1 Control Table

The truth table for the hazard detection unit is based on the input signals and determines the output signals. Here is the simplified truth table:

| i_jmp_stop | i_if_id_funct == `CODE_FUNCT_JALR` or `CODE_FUNCT_JR` | i_if_id_op == `CODE_OP_R_TYPE` | i_if_id_op == `CODE_OP_BNE` or `CODE_OP_BEQ` | o_jmp_stop |
|------------|------------------------------------------------------|--------------------------------|---------------------------------------------|------------|
| `0`          | `1`                                                    | `1`                              | `-`                                           | `1`          |
| `0`          | `-`                                                    | `-`                              | `1`                                           | `1`          |
| `1 or -`     | `-`                                                    | `-`                              | `-`                                           | `0`          |

| i_if_id_op == `CODE_OP_HALT` | o_halt |
|-----------------------------|--------|
| `1`                           | `1`      |
| `0`                           | `0`      |

| i_id_ex_rt == i_if_id_rs or i_if_id_rd | i_id_ex_op == `CODE_OP_LW` or `CODE_OP_LB` or `CODE_OP_LBU` or `CODE_OP_LH` or `CODE_OP_LHU` or `CODE_OP_LUI` or `CODE_OP_LWU` | o_jmp_stop | o_not_load |
|----------------------------------------|------------------------------------------------------------------------------------------------------------------|------------|------------|
| `1`                                      | `1`                                                                                                                  | `-`          | `1`          |
| `-`                                      | `-`                                                                                                                  | `1`          | `1`          |

### 3.4. Processor Operation
To operate the processor, a program must first be loaded into the instruction memory. Then, the processor must be started to execute the program. The processor will stop automatically when the `halt` instruction is executed. To make this possible, a `uart` module was implemented to allow communication with the processor via a serial terminal, and a `debugger` module controls communication between the processor and the `uart` module.

#### 3.4.1. UART

<p align="center">
  <img src="imgs/UART.png" alt="UART">
</p>

This Verilog module defines a [UART (Universal Asynchronous Receiver-Transmitter)](https://en.wikipedia.org/wiki/Universal_Asynchronous_Receiver-Transmitter), which is a device used for asynchronous serial communication between digital devices.

The `uart` module has several configurable parameters, including the number of data bits (`DATA_BITS`), the number of ticks for the start bit (`SB_TICKS`), the precision bit for the baud rate (`DVSR_BIT`), the baud rate divisor (`DVSR`), and the FIFO size (`FIFO_SIZE`).

The module's inputs include the clock signal (`clk`), reset signal (`reset`), read and write signals for UART (`rd_uart` and `wr_uart`), the receive signal (`rx`), and the data to write (`w_data`).

The module's outputs include the full and empty transmission and reception flags (`tx_full` and `rx_empty`), the transmission signal (`tx`), and the read data (`r_data`).

The `uart` module consists of several submodules:

- `uart_brg`: Generates the tick signal for the baud rate.
- `uart_rx`: Handles the data reception.
- `uart_tx`: Handles the data transmission.
- `fifo_rx_unit` and `fifo_tx_unit`: FIFO queues to store received and transmitted data, respectively.

Each submodule has its own configuration and input/output signals, which are connected to the inputs and outputs of the `uart` module to form a complete UART communication system.

#### 3.4.2. Debugger

<p align="center">
  <img src="imgs/Debugger.png" alt="Debugger">
</p>

The `debugger` module is used for debugging and controlling the execution of a MIPS processor via the UART interface. It allows loading programs into the processor's instruction memory, step-by-step execution, viewing registers and memory data, as well as communication with a development environment via the UART interface.

The module's outputs include:

- **o_uart_wr:** Write signal for the UART interface.
- **o_uart_rd:** Read signal for the UART interface.
- **o_mips_instruction:** Current instruction executed by the processor.
- **o_mips_instruction_wr:** Indicates if there is an instruction to write to the instruction memory.
- **o_mips_flush:** Signal to perform a flush in the processor.
- **o_mips_clear_program:** Signal to clear the program in the processor.
- **o_mips_enabled:** Indicates if the processor is enabled for execution.
- **o_uart_data_wr:** Data to write to the UART interface.
- **o_status_flags:** Various status signals, including memory and execution status.

Its main functions are:

1. **State Control:**
   - The module has a set of states that determine its behavior based on the input and output signals.

2. **Program Loading:**
   - In the initial state (`DEBUGGER_STATE_IDLE`), the debugger waits for commands via the UART interface.
   - It can load programs into the processor's instruction memory (`DEBUGGER_STATE_LOAD`).
   - Commands like "L" are detected to load a program.

3. **Program Execution:**
   - It can start the execution of the program on the processor (`DEBUGGER_STATE_RUN`).
   - It allows step-by-step execution with commands ("S" for a step, "E" for continuous execution).

4. **Viewing Registers and Memory:**
   - It shows the content of registers and memory in the `DEBUGGER_STATE_PRINT_REGISTERS` and `DEBUGGER_STATE_PRINT_MEMORY_DATA` states, respectively.
   - The information is sent through the UART interface for display in a development environment.

5. **UART Communication:**
   - It allows reading and writing data through the UART interface.
   - It manages read and write buffers for UART communication.

6. **Flow Control:**
   - It controls the flow of program execution based on the received commands and program conditions.

## 4. Simulations
To ensure the correct functioning of the MIPS system, a series of simulations have been conducted using the integrated simulation tool in Vivado. These simulations allow us to observe the system's behavior in a controlled environment and verify that all parts of the system work as expected.

For each component of the system, we have created a specific *Test Bench*. A *Test Bench* is a simulation environment used to verify the behavior of a module under different input conditions. Each *Test Bench* generates a set of input signals for the module being tested and then observes the output signals to verify that the module behaves as expected.

### 4.1. `IF` Stage Tests

#### 4.1.1 Test Bench for the `PC` Module

The `pc` module is responsible for generating the address of the next instruction to execute. To test this module, we created a *Test Bench* that performs several tests:

1. **Increment Test:** This test verifies that the `o_pc` signal increments correctly when the `i_next_pc` signal increments and `i_enable` is active. `i_next_pc` is incremented ten times, and `o_pc` is expected to be 10.

2. **Disable Test:** This test verifies that the `o_pc` signal does not increment when `i_enable` is inactive. `i_next_pc` is incremented ten times with `i_enable` inactive, and `o_pc` is expected to remain 10.

3. **Stop Test:** This test verifies that the `o_pc` signal does not increment when `i_halt` is active. `i_halt` is activated, `i_next_pc` is incremented five times, and `o_pc` is expected to remain 10.

4. **Reset Test:** This test verifies that the `o_pc` signal resets correctly when `i_flush` is activated. `i_flush` is activated, `i_next_pc` is incremented ten times, and `o_pc` is expected to be 35.

5. **No Load Test:** This test verifies that the `o_pc` signal does not increment when `i_not_load` is active. `i_not_load` is activated, `i_next_pc` is incremented five times, and `o_pc` is expected to remain 35.

6. **Load Test:** This test verifies that the `o_pc` signal increments correctly when `i_not_load` is inactive. `i_not_load` is deactivated, `i_next_pc` is incremented five times, and `o_pc` is expected to be 45.

7. **Final Stop Test:** This test verifies that the `o_pc` signal does not increment when `i_halt` is active. `i_halt` is activated, and `o_pc` is expected to remain 45.

In each test, if the output matches the expected result, a message indicating the test passed is displayed. Otherwise, an error message is shown.

#### 4.1.2. Test Bench for the `Instruction Memory` Module

The `instruction_memory` module is responsible for storing and retrieving instructions. To test this module, we created a *Test Bench* that performs several tests:

1. **Write Test:** This test verifies that instructions can be written to memory. Random instructions are generated and written to memory. The written instruction is shown in each cycle.

2. **Read Test:** This test verifies that instructions can be read from memory. Instructions are read from memory in the same order they were written, and the instructions are displayed.

3. **Clear Test:** This test verifies that memory can be cleared correctly. The `i_clear` signal is activated to clear the memory, and the instructions are read again to verify the memory has been cleared. Then, instructions are written and read again to verify that memory can be reused.

In each test, the written or read instruction is displayed to verify that the operation was performed correctly.

#### 4.1.3. Integration Test Bench
The `IF` module is responsible for the instruction fetch stage of the pipeline. To test this module, we have created a *Test Bench* that performs several tests:

1. **Test 0**: Write 40 random instructions to memory and then write a halt instruction.

2. **Test 1**: Read the first 10 instructions from memory and display the instruction and the program counter (PC).

3. **Test 2**: Disable the `i_enable` signal and read the next 5 instructions. Since `i_enable` is disabled, the PC should not increment.

4. **Test 3**: Enable the `i_enable` and `i_halt` signals and read the next 5 instructions. Since `i_halt` is enabled, the PC should not increment.

5. **Test 4**: Disable the `i_halt` signal and read the next 10 instructions. The PC should increment after each read.

6. **Test 5**: Enable the `i_not_load` signal and read the next 5 instructions. Since `i_not_load` is enabled, the PC should not increment.

7. **Test 6**: Disable the `i_not_load` signal, reset the PC to 0, and read the next 10 instructions. The PC should increment after each read.

8. **Test 7**: Change the next PC source to `i_next_not_seq_pc` and read the next 5 instructions. The PC should be equal to `i_next_not_seq_pc` after the first read, and then increment after each subsequent read.

9. **Test 8**: Clear the memory, reset the PC to 0, and read the first 5 instructions. Since the memory has been cleared, all instructions read should be 0.

In each test, the instruction read and the value of the PC are displayed to verify that the operation has been performed correctly.

### 4.2. `ID` Stage Tests

#### 4.2.1 Test Bench for the `Register Bank` module
The `registers_bank` module is responsible for storing and retrieving data from registers. To test this module, we have created a *Test Bench* that performs several tests:

1. **Write Test**: This test verifies that data can be written to the registers. Random data is generated and written to the registers. The written data and the address of each register are displayed.

2. **Read Test**: This test verifies that data can be read from the registers. The data from registers A and B are read and displayed. The register addresses for buses A and B increment and decrement, respectively, with each cycle.

In each test, the data read or written and the register address are displayed to verify that the operation has been performed correctly.

#### 4.2.2. Test Bench for the `Main Control` module

**Testbench Description:**
The `main_control` module is responsible for generating control signals to orchestrate the operation of the processor. To test this module, we have created a *Test Bench* that performs several tests:

1. **R-Type Instructions:**
   - Tests for arithmetic and logical operations (add, sub, and, or, xor, nor, slt).
   - Tests for logical and arithmetic shifts (sll, srl, sra).
   - Tests for unsigned operations (addu, subu).
   - Tests for variable logical shifts (sllv, srlv, srav).
   - Tests for jumps and calls (jalr, jr).

2. **I-Type Instructions:**
   - Tests for data load and store operations (lw, sw).
   - Tests for conditional branches (beq, bne).
   - Tests for immediate arithmetic operations (addi).
   - Tests for immediate logical operations (andi, ori, xori).
   - Tests for immediate comparisons (slti).
   - Test for loading a constant into the upper part of the register (lui).
   - Tests for loading bytes, halfwords, and words from memory (lb, lbu, lh, lhu, lwu).

3. **J-Type Instructions:**
   - Tests for unconditional jumps (j, jal).

4. **Branch Conditions:**
   - Tests for branch conditions in branch instructions (beq, bne).
   - Verification of control signals based on comparison results.

5. **Special Scenarios:**
   - Tests with nop instructions and special conditions.

Each test verifies that the control signals generated (`o_ctrl_regs`) match the expected values for each input configuration. This approach ensures comprehensive coverage of the `main_control` module's behavior in various contexts, ensuring its correct functionality.

#### 4.2.3. Integration Test Bench
The `ID` module is responsible for the instruction decoding stage of the pipeline. To test this module, we have created a *Test Bench* that performs several tests:

1. **Loading Random Instructions:** Random instructions are generated and loaded into the `id` module. Then, it is observed if the module's outputs match expectations. This includes cases for both R-type and I-type instructions with different functions.

2. **Specific Instruction Tests:** Specific tests are carried out for various instructions such as `BEQ`, `BNE`, `LW`, `SW`, `J`, `JAL`, etc. The module's outputs are expected to match the expected results for each of these instructions. The correct generation of control signals and the values of the outputs are verified.

3. **Handling Exceptions and NOP Case:** The handling of the `NOP` (No Operation) instruction is also tested, where the module should generate specific control signals and make no changes to the processor's state. Additionally, it is verified that exceptions and jumps are handled correctly.

4. **Results Comparison:** After each instruction is executed, the outputs generated by the module are compared with the expected results. If there is any discrepancy, an error message with specific details is displayed to aid in debugging.

5. **Data Handling and Arithmetic Operations:** Instructions involving data transfer (Load and Store), as well as arithmetic and logical operations, are tested. The outputs should correctly reflect the operations performed by the module.

### 4.3. `EX` Stage Tests

#### 4.3.1. Test Bench for the `ALU` module
The `alu` module is responsible for performing arithmetic and logical operations. To test this module, we have created a *Test Bench* that performs several tests:

1. **Instruction Tests:** These verify the correct operation of individual instructions, including the `NOP` (No Operation) instruction and the handling of exceptions and jumps.

2. **Results Comparison:** After each instruction, the results generated by the module are compared with the expected results. If there are discrepancies, details are provided to aid in debugging.

3. **Data Handling and Arithmetic Operations:** Instructions involving data transfer and arithmetic and logical operations are tested. The outputs should correctly reflect the operations performed by the module.

#### 4.3.2. Test Bench for the `ALU Control` module
The `alu_control` module is responsible for generating the control signal for the ALU unit. To test this module, a series of tests are performed, providing all possible combinations of input signals to the ALU control module. This ensures full coverage of the tests, as all possible scenarios are examined. After each test, the control signal generated by the ALU control module is verified. If the control signal is as expected, the test is considered successful, and a message indicating the test passed is printed. If the control signal is not as expected, the test is considered failed, and a message indicating the test failed is printed.

#### 4.3.3. Integration Test Bench
Due to the complexity of implementing an integration *Test Bench* for this stage and because the test itself does not add much value to the project, it has been decided not to perform it.

### 4.4. `MEM` Stage Tests

#### 4.4.1. Test Bench for the `Data Memory` module
The `data_memory` module is responsible for reading and writing data to memory. The test bench performs the following:

1. **Write and Read Tests:** 10 random values are written to memory and then read. The values and memory addresses are displayed for each write and read operation.

2. **Debug Bus Test:** Displays the data from all memory addresses on the debug bus.

#### 4.4.2. Integration Test Bench
The `mem` module is responsible for reading and writing data to memory. The test bench performs the following:

1. **Write and Read Tests:** 20 random values are written to memory with different write sources (`i_mem_wr_src`) and then read with different read sources (`i_mem_rd_src`). The values, memory addresses, and write and read sources are displayed for each write and read operation.

2. **Debug Bus Test:** Displays the data from all memory addresses on the debug bus.

### 4.5. `WB` Stage Tests

#### 4.5.1. Integration Test Bench
The `wb` module is responsible for selecting the data that will be written to the registers. The test bench performs the following:

1. **Data Selection Tests:** Two random values are generated for `i_alu_result` and `i_mem_result`. Then, the value of `i_mem_to_reg` is changed to select between `i_alu_result` and `i_mem_result`.

2. **Verification of Results:** It checks if `o_wb_data` is equal to `i_mem_result` when `i_mem_to_reg` is 0, and if `o_wb_data` is equal to `i_alu_result` when `i_mem_to_reg` is 1. If the values are equal, a success message is printed. If they are not equal, an error message is printed.

### 4.6. `Hazards Units` Tests

#### 4.6.1. Test Bench for the `Short Circuit` module
The `short_circuit` module is responsible for forwarding data in the pipeline. The test bench performs the following:

1. **Short Circuit Tests:** Random addresses are generated and assigned to the input signals `i_ex_mem_addr`, `i_id_ex_rs`, `i_mem_wb_addr`, and `i_id_ex_rt`. Then, the values of `i_ex_mem_wb` and `i_mem_wb_wb` are changed to simulate different short circuit conditions.

2. **Verification of Results:** It checks if `o_sc_data_a_src` and `o_sc_data_b_src` are equal to the expected codes for each short circuit condition. If the codes are equal, a success message is printed. If they are not equal, an error message is printed.

#### 4.6.2. Test Bench for the `Risk Detection` module
The `risk_detection` module is responsible for detecting and handling situations that could cause issues in the processor's pipeline, such as data and control hazards. The test bench performs the following:

1. **Risk Detection Tests:** The input signals are configured to simulate different risk conditions, including load hazards (`Load Hazard`), jump stops (`Jump Stop`), and halts (`Halt`).

2. **Verification of Results:** It checks if the output signals `o_not_load`, `o_jmp_stop`, and `o_halt` are equal to the expected values for each risk condition. If the values are equal, a success message is printed. If they are not equal, an error message is printed.

### 4.7. Other Tests
Other auxiliary modules used in the processor implementation, such as the `mux` module or the `adder` module, are commonly used across multiple stages and have their corresponding *Test Benches* to verify their correct operation. However, due to their simplicity, the details of these tests are not included in this document. On the other hand, the implementation of the *Test Bench* for the `debugger` module has been considered unnecessary since its functionality testing is covered by the complete system integration *Test Bench* detailed in later sections. Finally, the `uart` module comes from an external repository where it has already undergone complete functional testing, so a *Test Bench* for it is not implemented. Likewise, the testing of this module is also covered by the complete system integration *Test Bench*.

### 4.8. `Mips` Integration Test
The `mips` module is responsible for integrating all the stages of the pipeline. A full processor integration *Test Bench* has been created for this module. This test was performed to verify the correct operation of all parts together. The test bench performs the following:

1. **Test Programs:**
   - Three programs (`first_program`, `second_program`, `third_program`) are defined with MIPS instructions represented by macros (`ADDI`, `J`, `SW`, etc.).
   - Each program demonstrates various operations and situations, such as arithmetic operations, jumps, load and store operations, and subroutine calls.

2. **Program Execution:**
   - The programs are executed sequentially in the MIPS processor (`mips`).
   - Each instruction is loaded into the instruction memory and executed in clock cycles.

3. **Continuous Monitoring:**
   - During execution, changes in registers and data memory are monitored.
   - It is verified that the changes in registers and memory match the expectations.

4. **Program Completion:**
   - The `o_end_program` signal is monitored to determine when the execution of a program finishes.
   - Relevant information is recorded and displayed after each program finishes.

5. **Reset and Flush:**
   - The processor’s reset (`i_reset`) and flush (`i_flush`) functionality are tested.

6. **Control Flow:**
   - Control flow scenarios, such as unconditional jumps (`J`), subroutine calls (`JAL`, `JALR`), and conditional jumps (`BEQ`, `BNE`), are tested.

7. **Handling Halts:**
   - The detection and handling of the halt instruction (`HALT`) are tested.
   - It is verified that execution stops correctly.

8. **Memory Verification:**
   - It is verified that load and store operations (`LW`, `SW`, `LBU`, etc.) are correctly performed in data memory.

9. **Temporal Monitoring:**
   - The state of registers and memory is monitored at regular time intervals.

10. **Console Results:**
    - Detailed information is shown in the console for each change in registers and memory.
    - The information includes the cycle number (`j`), the affected register or memory address, and the values before and after the change.

#### 4.8.1. Annex 1: Test Programs

##### 4.8.1.1. Program 1
```assembly
ADDI R4,R0,7123
ADDI R3,R0,85
ADDU R5,R3,R4
SUBU R6,R4,R3
AND  R7,R4,R3
OR   R8,R3,R4
XOR  R9,R4,R3
NOR  R10,R4,R3
SLT  R11,R3,R4
SLL  R12,R10,2
SRL  R13,R10,2
SRA  R14,R10,2
SLLV R15,R10,R11
SRLV R16,R10,R11
SRAV R17,R10,R11
SB   R13,4(R0)
SH   R13,8(R0)
SW   R13,12(R0)
LB   R18,12(R0)
ANDI R19,R18,6230
LH   R20,12(R0)
ORI  R21,R20,6230
LW   R22,12(R0)
XORI R23,R22,6230
LWU  R24,12(R0)
LUI  R25,6230
LBU  R26,12(R0)
SLTI R27,R19,22614
HALT
```

##### 4.8.1.2. Program 2
```assembly
J	5
ADDI	R3,R0,85
NOP
NOP
NOP
JAL 	8
ADDI	R4,R0,95
NOP
ADDI	R5,R0,56
JR	R5
ADDI	R2,R0,2
NOP
NOP
NOP
ADDI	R6,R0,80
JALR	R30,R6
ADDI	R1,R0,0
NOP
NOP
NOP
ADDI	R7,R0,15
ADDI	R8,R0,8
ADDI	R8,R8,1
SW	R7,0(8)
BNE	R8,R7,-3
BEQ	R8,R7,2
ADDI	R9,R0,8
ADDI	R10,R0,8
ADDI	R11,R0,8
HALT
```

##### 4.8.1.3. Program 3
```assembly
ADDI	R3,R0,85
JAL	14
ADDI	R4,R0,86
J	16
NOP
NOP
NOP
NOP
NOP
NOP
NOP
NOP
NOP
NOP
ADDI	R5,R0,87
JALR	R30,R31
HALT
```

#### 4.8.2. Annex 2: Execution Results

##### 4.8.2.1. Program 1 Execution

<p align="center">
  <img src="imgs/TB_MIPS_PG1.png" alt="UART">
</p>

##### 4.8.2.2. Program 2 Execution

<p align="center">
  <img src="imgs/TB_MIPS_PG2.png" alt="UART">
</p>

##### 4.8.2.3. Program 3 Execution

<p align="center">
  <img src="imgs/TB_MIPS_PG3.png" alt="UART">
</p>

### 4.9. System Test
The `top` module is responsible for integrating all parts of the system (*MIPS*, *Debugger*, and *UART*). A complete system *Test Bench* has been created for this module. The test bench performs the following:

1. **Test Program:**
   
   - A program of instructions (`first_program`) is defined, represented by macros (`ADDI`, `SB`, `LW`, etc.). The test program used is [program 1](#4811-programa-1) from the integration tests of the `mips` module.

   - The program simulates a sequence of MIPS operations, and the results are transmitted and received via the serial interface.

2. **Transmission and Reception Tasks:**
   - Tasks (`send` and `receive`) are implemented to simulate the transmission and reception of data over the serial interface.

   - The `send` task simulates the transmission of a data set.

   - The `receive` task simulates the reception of data.

3. **Test Sequence:**
   - An initial command is sent via the serial interface to indicate the system to start receiving instructions.

   - The MIPS instructions defined in `first_program` are sent through the serial interface.

   - An execution command is sent via the serial interface to instruct the system to begin executing the instructions.

   - The system is expected to process the instructions and send the results back.

4. **Reception and Display of Results:**

   - After the instruction transmission, a reception cycle begins to receive the processed results from the system.

   - The content of `r_data`, which contains the received results, is displayed in the console.
     
## 5. Results

Finally, the bitstream of the complete system is generated and loaded onto the FPGA. The FPGA is connected to a computer via a USB cable, and a test program is run on the computer to verify the system's operation. The system was tested with the three test programs defined in the previous section. It was confirmed that the system worked correctly and that the obtained results were as expected. 

### 5.1. Program 1

<p align="center">
  <img src="imgs/Execute_PG1.png" alt="UART">
</p>

### 5.2. Program 2

<p align="center">
  <img src="imgs/Execute_PG2.png" alt="UART">
</p>

### 5.3. Program 3

<p align="center">
  <img src="imgs/Execute_PG3.png" alt="UART">
</p>

## 6. References

- Patterson, D. A., & Hennessy, J. L. (2013). *Computer Organization and Design MIPS Edition: The Hardware/Software Interface*. Morgan Kaufmann.

- Hennessy, J. L., & Patterson, D. A. (2011). *Computer Architecture: A Quantitative Approach*. Morgan Kaufmann.

- Kane, G., & Heinrich, J. (1992). *MIPS RISC Architecture*. Prentice Hall.

- Sweetman, D. (2007). *See MIPS Run*. Morgan Kaufmann.
