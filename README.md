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
