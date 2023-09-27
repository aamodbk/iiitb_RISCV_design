# RISC-V Microarchitecture Design
## Day 1
### Installation of Required Tools
First clone the following Github repository into your main working directory.
```
git clone https://github.com/kunalg123/riscv_workshop_collaterals.git
```

Next type in the following commands to install the RISCV GNU toolchain.
```
sudo apt install libboost-all-dev
cd riscv_workshop_collaterals
chmod +x run.sh
./run.sh
```

Ignore the error shown at the end of compilation and type the following.
```
cd ~/riscv_toolchain/iverilog/
git checkout --track -b v10-branch origin/v10-branch
git pull
chmod 777 autoconf.sh
./autoconf.sh
./configure
make
sudo make install
```

Next, to set the $PATH variable in `.bashrc` type the following into the terminal.
```
gedit .bashrc
export PATH="/home/[your_username]/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH"
# Close the .bashrc and type in terminal
source .bashrc
```

### RISC-V ISA
The RISC-V (Reduced Instruction Set Computer - Five) ISA (Instruction Set Architecture) is an open-source and royalty-free architecture for designing and implementing processors. It is designed to be simple, modular, and highly customizable, allowing it to be tailored for various applications and performance levels.
RISC-V instructions are designed to be concise, regular, and easy to decode, contributing to efficient hardware implementation and compiler optimization. The instructions are grouped into several standard base configurations, each designed for specific data widths and addressing capabilities:
* RV32I/RV64I/RV128I: These are the base integer instruction sets for 32-bit, 64-bit, and 128-bit data widths, respectively. They include fundamental operations for arithmetic, logic, and control flow, such as add, subtract, shift, jump, and branch instructions.
* RV32M/RV64M/RV128M: The "M" extension adds multiply and divide instructions to the base integer instruction sets, useful for applications requiring higher computational capabilities.
* RV32F/RV64F/RV128F: The "F" extension introduces single-precision floating-point instructions for performing floating-point arithmetic and conversions.
* RV32D/RV64D/RV128D: The "D" extension complements the "F" extension by adding double-precision floating-point instructions, suitable for applications demanding higher precision.
Overall, the RISC-V instruction sets are designed to offer a robust foundation for building processors that can meet the needs of a wide range of applications, from embedded systems to high-performance computing, by providing a well-defined set of operations that can be executed by the hardware.

### RISC-V GNU Compile Toolchain
To test the toolchain create a file `sum1ton.c` in the main directory.

```
#include <stdio.h>

int main()
{
    int i, sum = 0, n = 19;
    for(i=1; i<=n; i++){
        sum+=i;
    }
    printf("The sum of numbers from 1 to %d is %d\n", n, sum);
    return 0;
}
```
Test the above program with the `gcc` compiler and run the executable to verify it.
Next, to compile the program with the RISC-V toolchain and view the assembly code, type the following commands into the terminal.

```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton_O1.o sum1ton.c
riscv64-unknown-elf-objdump -d sum1ton_O1.o | less
```
![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/assmain.png)

The number of instructions for the main program is seen as 14 from the above image.
To execute the assembly instructions, type the following.

```
spike pk sum1ton_O1.o
```
To debug the code using the spike tool, type in the following into the terminal.

```
spike -d pk sum1ton_O1.o
```
Upon entering this line, code can be manually debugged, part of it can be run and contents of registers can be checked.
The above code can also be compiled in a different mode/optimization level as shown below.

```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton_Ofast.o sum1ton.c
riscv64-unknown-elf-objdump -d sum1ton_Ofast.o | less
```
![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/assmain2.png)

As seen from the above image, the number of assembly instructions required for the main program has reduced to 11.

### Number system in RISC-V
The RISC-V architecture employs well-defined specifications for its number system representations. For integers, RISC-V offers both signed and unsigned representations with various data widths, such as 8-bit, 16-bit, 32-bit, and 64-bit, providing flexibility to accommodate different ranges of whole numbers. The integer representations adhere to two's complement and sign-magnitude formats, allowing efficient arithmetic operations. For floating-point numbers, RISC-V adheres to the IEEE 754 standard, supporting both single-precision (32-bit) and double-precision (64-bit) formats.
These specifications enable consistent and reliable data manipulation, making RISC-V processors versatile and capable of handling a broad spectrum of numerical tasks with precision and efficiency.

Implement the below C programs using the RISC-V compiler tool and execute them to find the lower and upper limits for the signed and unsigned values in the number system.

**Unsigned**
```
#include <stdio.h>
#include <math.h>

int main()
{
    unsigned long long int max = (unsigned long long int)(pow(2, 64)-1);
    unsigned long long int min = (unsigned long long int)(pow(2, 64)*-1);
    printf("Highest number represented by unsigned long long int is %llu \n", max);
    printf("Lowest number represented by unsigned long long int is %llu \n", min);
    return 0;
}
```

**Signed**
```
#include <stdio.h>
#include <math.h>

int main()
{
    unsigned long long int max = (long long int)(pow(2, 64)-1);
    unsigned long long int min = (long long int)(pow(2, 64)*-1);
    printf("Highest number represented by long long int is %lld \n", max);
    printf("Lowest number represented by long long int is %lld \n", min);
    return 0;
}
```

The results of the above codes are as shown below.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/unsigned.png)

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/signed.png)

## Day 2
### Application Binary Interface (ABI)

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/ABI.png)

Application Binary Interface (ABI) refers to a standardized interface that defines how software interacts with a RISC-V processor and the underlying system. The RISC-V ABI encompasses rules and conventions for various aspects, including function calling conventions, parameter passing, register usage, memory layout, and system calls. The ABI defines important details, such as how function arguments are passed, which registers are preserved across function calls, and how exceptions and system calls are handled.
In RISC V architecture, the width of the register is defined as XLEN. For RV64 and RV32, the widths are 64 bits and 32 bits, respectively.

The ABI uses 32 different registers which are each of width XLEN = 32 bit for RV32 (XLEN = 64 for RV64). On a higher level of abstraction these registers are accessed by their respective ABI names.
For base integer instructions there are mainly 3 types of of such registers:
* I-Type (Immediate Type) - These instructions have an immediate (constant) value as one of their operands, and they work with a source register to perform operations like arithmetic, logical, and memory operations.
* R-Type (Register Type) - These instructions involve operations that operate on two source registers and store the result in a destination register. They include arithmetic, logical, and bitwise operations.
* S-Type (Store Type) - S-type instructions are used for storing data into memory. They combine a source register, a destination address (base register), and an immediate offset to determine where the data should be stored.

Below image shows all base instruction formats for RISC-V.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/isa.png)

Re-writing the previous `sum1ton.c` code, we create a new file `1to9_custom.c` as follows.
```
#include <stdio.h>

extern int load(int x, int y); 

int main() {
	int result = 0;
    int count = 2;
    result = load(0x0, count+1);
    printf("Sum of number from 1 to %d is %d\n", count, result); 
}
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/algo.png)

Following the above algorithm, we also create an assembly file `load.S`.
```
.section .text
.global load
.type load, @function

load:
	add 	a4, a0, zero //Initialize sum register a4 with 0x0
	add 	a2, a0, a1   // store count of 10 in register a2. Register a1 is loaded with 0xa (decimal 10) from main program
	add	a3, a0, zero // initialize intermediate sum register a3 by 0
loop:	add 	a4, a3, a4   // Incremental addition
	addi 	a3, a3, 1    // Increment intermediate register by 1	
	blt 	a3, a2, loop // If a3 is less than a2, branch to label named <loop>
	add	a0, a4, zero // Store final result to register a0 so that it can be read by main program
	ret
```

Next, compile the mentioned C program along with the ASM file using the RISC-V compiler tool.
```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o 1to9_custom_Ofast.o 1to9_custom.c load.S
spike pk 1to9_custom_Ofast.o
```
The result of the execution is as shown below.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/loadres.png)

The compiled assembly code of the main program is as shown below.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/asm.png)

### Base Verification Flow
Here we simulate and test the written C code on a RISC-V netlist written in verilog by converting the C code into HEX format and then running it on the RISC-V CPU which then displays the results.
Type the following commands into the terminal to run.

```
chmod 777 rv32im.sh
./rv32im.sh
```

The result of the above execution is as follows.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/rv32im.png)

## Day 3
### TL Verilog
Transactional-Level Verilog (TL-Verilog) is an extension of the traditional Verilog hardware description language that aims to simplify and enhance the design process for digital circuits. TL-Verilog introduces higher-level abstractions that allow designers to express complex functionalities more concisely and intuitively compared to traditional RTL (Register Transfer Level) design.
TL-Verilog is the easiest way to write and edit Verilog with fewer bugs and is supported by Makerchip IDE which we will be using next.

### Makerchip IDE
Makerchip IDE is an integrated development environment designed for digital design and hardware description using modern hardware description languages like TL-Verilog and traditional Verilog. It provides a user-friendly web-based platform that combines coding, simulation, and visualization tools to facilitate the design and verification of digital circuits.
It's particularly known for its support of TL-Verilog, allowing designers to leverage its higher-level abstractions for more efficient and intuitive circuit description.
Below shown is an example of pythagoras equation in Makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerpyth.png)

Next, to make an inverter in the IDE, type the following.
```
$out = !$in;
```
The result is as follows.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerinv.png)

To make an AND gate.
```
$out = $in1 && $in2;
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerand.png)

To make an OR gate.
```
$out = $in1 || $in2;
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makeror.png)

To make an XOR gate.
```
$out = $in1 ^ $in2;
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerxor.png)

To perform 4-bit vector addition.
```
$out[4:0] = $in1[3:0] + $in2[3:0];
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makervec.png)

To make a 2:1 8-bit MUX gate.
```
$out[7:0] = $sel ? $in1[7:0] : $in0[7:0];
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makermux.png)

To make a calculator to perform arithmetic operations.
```
$op[1:0] = $rand[1:0];
$val1[31:0] = $rand1[3:0];
$val2[31:0] = $rand2[3:0];

$sum[31:0] = $val1 + $val2;
$diff[31:0] = $val1 - $val2;
$prod[31:0] = $val1 * $val2;
$div[31:0] = $val1 / $val2;

$out[31:0] = $op[1] ? ($op[0] ? $div : $prod):($op[0] ? $diff : $sum);
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makercalc.png)

### Sequential Circuits
To make a Fibonnaci series generator.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/fib.png)
```
$num[31:0] = $reset ? 1 : (>>1$num + >>2$num);
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerfib.png)

To make a Free running counter.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/frc.png)
```
$cnt[31:0] = $reset ? 0 : (>>1$cnt + 1);
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerfrc.png)

To make a combinatorial calculator and add sequential integration to it.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/seqcalc.png)
```
$op[1:0] = $rand[1:0];
$val1[31:0] = >>1$out;
$val2[31:0] = $rand2[3:0];

$sum[31:0] = $val1 + $val2;
$diff[31:0] = $val1 - $val2;
$prod[31:0] = $val1 * $val2;
$div[31:0] = $val1 / $val2;

$out[31:0] = $op[1] ? ($op[0] ? $div : $prod):($op[0] ? $diff : $sum);
```

The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerseqcalc.png)

### Pipelining
Pipelining or timing abstract is an important feature in TL verilog as it can be implemented very easily with fewer codes as compared to system verilog which reduces bugs to a great extent.
An example of this is exhibited in the pipelined pythagorean example.
```
   $aa = $rand1[3:0];
   $bb = $rand2[3:0];

   |calc
      @1
         $aa_sq[7:0] = $aa[3:0] * $aa[3:0];
         $bb_sq[7:0] = $bb[3:0] * $bb[3:0];
      @2
         $cc_sq[8:0] = $aa_sq + $bb_sq;
      @3
         $cc[4:0] = sqrt($cc_sq);
```
The result is:

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/makerpippyth.png)

Another example is of a counter with a calculator using pipelining.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/counter_calc.png)

The below image shows the implementation in makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/counter_calc_makerchip.png)

Another example is of a 2 cycle calculator using pipelining.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/2_cyc_calc.png)

The below image shows the implementation in makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/2_cyc_calc_makerchip.png)

### Validity
Validity in TL-Verilog refers to a concept related to the modeling of data and signals in Transaction-Level Verilog. Transactions represent higher-level operations or tasks that the digital circuit should perform. The concept of validity is typically used in the context of transactions to indicate whether the data within a transaction is valid or not.

### Clock Gating
Clock gating is a power-saving technique used in digital circuit design, particularly in synchronous digital systems, to reduce dynamic power consumption by selectively disabling (gating) the clock signal to certain circuit elements when they are not actively needed. This technique is particularly useful in modern ICs and microprocessors where power efficiency is a critical concern.

### Examples of Validity
#### Distance Accumulator

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/dist_acc.png)

The below image shows the implementation in makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/dist_acc_makerchip.png)

#### 2 Cycle Calculator

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/2_cyc_val.png)

Below image shows the implementation in makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/2_cyc_val_makerchip.png)

#### Calculator with single value memory

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/sing_val_calc.png)

Below image shows the implementation in makerchip.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/sing_val_calc_makerchip.png)

## Day 4
### Basic RISC-V CPU Micro-architecture
Below image show sthe block diagram for a RISC-V CPU.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/riscv_block_diagram.png)

The following are the main components of the RISC-V CPU:
* **Program Counter (PC)**: The PC keeps track of the address of the next instruction to be fetched from memory. It is incremented after each instruction fetch.
* **Instruction Decode Unit (IDU)**: In the IDU, instructions are decoded to determine their type and operands. It also generates control signals for subsequent stages.
* **Instruction Fetch Unit (IFU)**: This unit is responsible for fetching instructions from memory, typically using the program counter (PC) to determine the address of the next instruction.
* **Data Memory Unit (DMU)**: The DMU is responsible for loading and storing data to and from memory. It handles load and store operations, cache accesses, and memory management.
* **ALU (Arithmetic Logic Unit)**: The ALU performs arithmetic and logical operations on data from the register file based on the instructions. It handles operations like addition, subtraction, bitwise logic, etc.
* **Register File**: The register file is a set of registers used for temporary storage of data and operands. RISC-V architectures typically have 32 general-purpose registers (e.g., x0, x1, ..., x31).

We will design a RISC-V microarchitecture following 3 stages of a pipeline, Fetch, Decode and Execute.

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/cpu_arch.png)

All codes for each stage will be filled in the template given below:
```
\m4_TLV_version 1d: tl-x.org
\SV
   // This code can be found in: https://github.com/stevehoover/RISC-V_MYTH_Workshop
   
   m4_include_lib(['https://raw.githubusercontent.com/BalaDhinesh/RISC-V_MYTH_Workshop/master/tlv_lib/risc-v_shell_lib.tlv'])

\SV
   m4_makerchip_module   // (Expanded in Nav-TLV pane.)
\TLV

   // /====================\
   // | Sum 1 to 9 Program |
   // \====================/
   //
   // Program for MYTH Workshop to test RV32I
   // Add 1,2,3,...,9 (in that order).
   //
   // Regs:
   //  r10 (a0): In: 0, Out: final sum
   //  r12 (a2): 10
   //  r13 (a3): 1..10
   //  r14 (a4): Sum
   // 
   // External to function:
   m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
   // Function:
   m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
   m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
   m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
   // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program
   
   // Optional:
   // m4_asm(JAL, r7, 00000000000000000000) // Done. Jump to itself (infinite loop). (Up to 20-bit signed immediate plus implicit 0 bit (unlike JALR) provides byte address; last immediate bit should also be 0)
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)

   |cpu
      @0
         $reset = *reset;



      // YOUR CODE HERE
      // ...

      // Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
      //       be sure to avoid having unassigned signals (which you might be using for random inputs)
      //       other than those specifically expected in the labs. You'll get strange errors for these.

   
   // Assert these to end simulation (before Makerchip cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
   
   // Macro instantiations for:
   //  o instruction memory
   //  o register file
   //  o data memory
   //  o CPU visualization
   |cpu
      //m4+imem(@1)    // Args: (read stage)
      //m4+rf(@1, @1)  // Args: (read stage, write stage) - if equal, no register bypass is required
      //m4+dmem(@4)    // Args: (read/write stage)
      //m4+myth_fpga(@0)  // Uncomment to run on fpga

   //m4+cpu_viz(@4)    // For visualisation, argument should be at least equal to the last stage of CPU logic. @4 would work for all labs.
\SV
   endmodule
```

The code for PC is as follows:
```
	$pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/pc_mc.png)

The code for Instruction Fetch (IF) is as follows:
```
      @1
         $imem_rd_en = !$reset;
         $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
         $instr[31:0] = $imem_rd_data[31:0];
      ?$imem_rd_en
         @1
            $imem_rd_data[31:0] = /imem[$imem_rd_addr]$instr;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/if_mc.png)

The code for Instruction Decode (ID) is as follows:
```
      @1
         $is_i_instr = $instr[6:2] ==? 5'b0000x ||
                       $instr[6:2] ==? 5'b001x0 ||
                       $instr[6:2] ==? 5'b11001 ||
                       $instr[6:2] ==? 5'b11100;
         
         $is_u_instr = $instr[6:2] ==? 5'b0x101;
         
         $is_r_instr = $instr[6:2] ==? 5'b01011 ||
                       $instr[6:2] ==? 5'b011x0 ||
                       $instr[6:2] ==? 5'b10100;
         
         $is_b_instr = $instr[6:2] ==? 5'b11000;
         
         $is_j_instr = $instr[6:2] ==? 5'b11011;
         
         $is_s_instr = $instr[6:2] ==? 5'b0100x;
         
         $imm[31:0] = $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                      $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                      $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], 1'b0} :
                      $is_u_instr ? {$instr[31:12], 12'b0} :
                      $is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0} :
                                    32'b0;
         $opcode[6:0] = $instr[6:0];
         
         $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20];
            
         $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15];
         
         $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
            
         $funct7_valid = $is_r_instr ;
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];
            
         $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         ?$rd_valid
            $rd[4:0] = $instr[11:7];
            
         $dec_bits [10:0] = {$funct7[5], $funct3, $opcode};
         $is_beq = $dec_bits ==? 11'bx_000_1100011;
         $is_bne = $dec_bits ==? 11'bx_001_1100011;
         $is_blt = $dec_bits ==? 11'bx_100_1100011;
         $is_bge = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
         $is_addi = $dec_bits ==? 11'bx_000_0010011;
         $is_add = $dec_bits ==? 11'b0_000_0110011;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/id_mc.png)

The code for Register File Read is as follows:
```
      @1
         $rf_wr_en = 1'b0;
         $rf_wr_index[4:0] = 5'b0;
         $rf_wr_data[31:0] = 32'b0;
         
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_index1[4:0] = $rs1;
         
         $rf_rd_en2 = $rs2_valid;
         $rf_rd_index2[4:0] = $rs2;
         
         $src1_value[31:0] = $rf_rd_data1;
         $src2_value[31:0] = $rf_rd_data2;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/rfr_mc.png)

The code for ALU is as follows:
```
      @1
         $result[31:0] = $is_addi ? $src1_value + $imm :
                         $is_add ? $src1_value + $src2_value :
                         32'bx ;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/alu_mc.png)

The code for Register File write is as follows:
```
      @1
         $rf_wr_en = $rd_valid && $rd != 5'b0;
         $rf_wr_index[4:0] = $rd;
         $rf_wr_data[31:0] = $result;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/rfw_mc.png)

The code for Branch instructions is as follows:
```
      @1
         $taken_branch = $is_beq ? ($src1_value == $src2_value):
                         $is_bne ? ($src1_value != $src2_value):
                         $is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                         $is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                         $is_bltu ? ($src1_value < $src2_value):
                         $is_bgeu ? ($src1_value >= $src2_value):
                                    1'b0;
         `BOGUS_USE($taken_branch)
         $br_tgt_pc[31:0] = $pc + $imm;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/branch_mc.png)

To test the full code with all stages included using testbench type the following line in the @1 stage:
```
*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9) ;
```

![alt text](https://github.com/aamodbk/iiitb_RISCV_design/blob/main/test_mc.png)

## Day 5


## Contributors
* Aamod B K
* Kunal Ghosh

## Acknowledgments
* Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.

## Contact Information
* Aamod B K, IMtech 2020, International Institute of Information Technology, Bangalore Aamod.BK@iiitb.ac.in
* Kunal Ghosh, Director, VSD Corp. Pvt. Ltd. kunalghosh@gmail.com

## References
* VSD workshop -- https://www.vsdiat.com/
* RISC-V ISA -- https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf
* TL Verilog -- https://www.tl-x.org/
