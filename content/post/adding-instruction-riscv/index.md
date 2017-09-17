+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Adding custom instruction to RISCV ISA and running it on gem5"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++

This is a tutorial on how to add an instruction to the RISCV ISA, how to write program with the special instruction. I will also talk about how to add the new instruction to RISCV assembler and how to execute it on gem5.


<!--more-->

First of all download and install the riscv-tools:

```bash
$ git clone https://github.com/riscv/riscv-tools.git
$ git submodule update --init --recursive
$ export RISCV=/path/to/install/riscv/toolchain
$ ./build.sh
```
This will build the riscv toolchain. Now we will add a "modulo" instruction to the ISA. The isntruction and its semantics are given below:

```bash
mod r1, r2, r3

Semantics:
R[r1] = R[r2] % R[r3]
```
Open the file riscv-opcodes/opcodes, here you will be able to see the various opcodes and instruction bits assigned to various instructions. Assigned an unused instruction to modulo inst. I am adding it ner the "add" instruction

```bash
add     rd rs1 rs2 31..25=0  14..12=0 6..2=0x0C 1..0=3                          
sub     rd rs1 rs2 31..25=32 14..12=0 6..2=0x0C 1..0=3 
mod     rd rs1 rs2 31..25=16 14..12=0 6..2=0x0C 1..0=3                         
sll     rd rs1 rs2 31..25=0  14..12=1 6..2=0x0C 1..0=3                          
slt     rd rs1 rs2 31..25=0  14..12=2 6..2=0x0C 1..0=3                          
sltu    rd rs1 rs2 31..25=0  14..12=3 6..2=0x0C 1..0=3                          
xor     rd rs1 rs2 31..25=0  14..12=4 6..2=0x0C 1..0=3                          
srl     rd rs1 rs2 31..25=0  14..12=5 6..2=0x0C 1..0=3                          
sra     rd rs1 rs2 31..25=32 14..12=5 6..2=0x0C 1..0=3                          
or      rd rs1 rs2 31..25=0  14..12=6 6..2=0x0C 1..0=3                          
and     rd rs1 rs2 31..25=0  14..12=7 6..2=0x0C 1..0=3 
```

Now run the following command:

```bash
cat opcodes-pseudo opcodes opcodes-rvc opcodes-rvc-pseudo opcodes-custom | ./parse-opcodes -c > ~/temp.h
```
Open file temp.h and you will find the following two lines:

```C
#define MATCH_MOD 0x20000033                                                    
#define MASK_MOD  0xfe00707f
```
Add these two lines in riscv-gnu-toolchain/riscv-binutils-gdb/include/opcode/riscv-opc.h

Now edit riscv-gnu-toolchain/riscv-binutils-gdb/opcodes/riscv-opc.c and add this line in the assignment of const struct riscv\_opcode riscv\_opcodes[]:

```C
const struct riscv_opcode riscv_opcodes[] =                                     
{                                                                               
/* name,      isa,   operands, match, mask, match_func, pinfo.  */              
{"unimp",     "C",   "",  0, 0xffffU,  match_opcode, 0 },                       
{"unimp",     "I",   "",  MATCH_CSRRW | (CSR_CYCLE << OP_SH_CSR), 0xffffffffU,  match_opcode, 0 }, /* csrw cycle, x0 */
{"ebreak",    "C",   "",  MATCH_C_EBREAK, MASK_C_EBREAK, match_opcode, INSN_ALIAS },
{"ebreak",    "I",   "",    MATCH_EBREAK, MASK_EBREAK, match_opcode, 0 },          
{"sbreak",    "C",   "",  MATCH_C_EBREAK, MASK_C_EBREAK, match_opcode, INSN_ALIAS },
{"sbreak",    "I",   "",    MATCH_EBREAK, MASK_EBREAK, match_opcode, INSN_ALIAS },
....
....
....
{"add",       "I",   "d,s,t",  MATCH_ADD, MASK_ADD, match_opcode, 0 }
{"mod",       "I",   "d,s,t",  MATCH_MOD, MASK_MOD, match_opcode, 0 }
....
....
```

Now compile the riscv-gnu-toolchain again and you are done.

Lets write a program and see if the RISCV assembler can use the mod instruction:

```C
#include <stdio,h>

int a[] = {11,22,33,44,55};
int b[] = {10,10,10,10,10};
int c[4];

int main(){
  int i;
  for ( i = 0; i < 4; i++ ){
     asm volatile
     (
       "mod   %[z], %[x], %[y]\n\t"
       : [z] "=r" (c[i])
       : [x] "r" (a[i]), [y] "r" (b[i])
     )  
  }
 
  for ( int i = 0; i < 4; i++ ){
      if ( c[i] != i+1 ){
         printf("\n[[FAILED]]\n");
         return -1;
      }
  }
  
  printf("\n[[PASSED]]\n");

  return 0;
}
```

Compile it using the modified RISCV compiler

```bash
$ riscv64-unknown-elf-gcc mod.c -o mod
```
Now do an object dump to see that the mod instruction is actually used:

```bash
$ riscv64-unknown-elf-objdump -dC mod > mod.dump
```

Now open mod.dump and you will be able to see that mod instruction has been used.

I will later add how you can modify the ISA simulator to test the newly added instruction and how you can modify gem5 to be able to exexcute this instruction.
