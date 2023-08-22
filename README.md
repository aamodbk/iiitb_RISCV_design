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

The number of instructions for the main program is seen as 15 from the above image.
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

As seen from the above image, the number of assembly instructions required for the main program has reduced.

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
