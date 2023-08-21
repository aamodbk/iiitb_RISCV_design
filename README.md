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
