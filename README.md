# 9-bit-CPU
##authors: Nicole Cahlander and Jack Chambers

This document describes the architecture of our 9-bit CPU design as represented
in the logisim cpu.circ file, and provides documentation of instructions our CPU is capable of handling.
Any information contained within this readme or the cpu.circ file is purely for educational purposes.

This CPU is based loosely on the MIPS architecture, and RISC architecture in general.
As denoted by the title, this CPU works on 9-bit instructions. If you're asking yourself "wtf why,"
it's because 8 bit was too small and 16 bit was too scary! If you think that's an unsatisfactory answer,
you're not going to be happy when we say this CPU only has TWO registers - 0 and 1!
This architecture is lean and mean, but can still handle basic instructions one would expect from a simple processor,
including...
- Adding!
- Subtracting!
- Writing and reading to/from memory!
- Instruction branching!
- Jumping!
and not much else!

Please continue your journey through our CPU and learn its secrets with the following section...

##<p align=center>INSTRUCTION SYNTAX</p>

##R-Type
| op    |  rd   | rs    | rt   | func  |
| :---: | :---: | :---: |:---: | :---: |
| 0 0 0 |   _   |   _   |  _   | _ _ _ |

Similar to the MIPS architecture, the first three bits in an instruction represent an op-code,
with R-Type instructions always having an op-code of 000.
The func field selects which R-type operation is to be performed on the registers.
The rs and rt fields are for selecting which register values should be read for
the operation, and rd is the register in which the return value of the operation will be stored in.
Please note that since this architecture is frighteningly lean, there are only TWO registers,
and thus at least one of the registers used in an R-Type instruction will always be overwritten.
Do not be frightened by this fact! If you are too scared to overwrite a register you probably didn't need to perform the operation anyway.

###BEHOLD THE R-TYPE FUNC OPTIONS
    0 0 0 – ADD (add the contents of rs and rt, then store result in rd)
    0 0 1 – SUB  (subtract rt from rs and store result in rd)
    0 1 0 – AND (bitwise AND the contents of rs and rt, then store the result in rd)
    0 1 1 – OR (bitwise OR the contents of rs and rt, then store result in rd)

###Example R-Type Instructions
    000 0 0 0 010 - bitwise AND the bits in register 0 with itself and store the result in register 0
    000 0 1 1 000 - ADD register 1 and 0 together and store the sum in register 1
    000 1 0 1 011 - bitwise OR register 1 with register 0 and store the result in register 1


##I-Type

I-Type instructions are always denoted by a 1 in the leftmost bit in an instruction,
and include branching, loading immediate values into registers, and load word from memory.
The structure of an I-Type instruction depends on the on the specific op code used.
For example, an li (load immediate) instruction follows this syntax:

###Load immediate (li)
| op   | rd   | immediate  |
| :---: |:---:| :------:|
| 1 0 0 | 1 | 1 0 0 1 0 |
(Load immediate value of 18 into register 1)


To store a register value in memory (sw in MIPS), use op-code 011. The next bit will be the target register,
and the remaining 5 bits are the address in memory where the value should be stored.

###Store word (sw)
| op       | rd  | address  |
| :---: |:---:| :------:|
| 0 1 1 | 0 | 0 1 0 1 |
(Store value at register 0 in memory address 5)
