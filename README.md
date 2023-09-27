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
