# 9-bit-CPU
### authors: Nicole Cahlander and Jack Chambers

This document describes the architecture of our 9-bit CPU design as represented
in the logisim cpu.circ file, and provides documentation of instructions our CPU is capable of handling.
Any information contained within this readme or the cpu.circ file is intended purely for educational purposes.

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

Instructions can be written and executed from within the logisim ROM module located on the Western edge of the circuit. This module requires all instructions to be written in hexadecimal, but as is apparent our documentation and designed in binary. Alas, if this is nauseating to you, please feel free to use this handy converter when (not if) you begin writing your own programs:

  [ALL SHALL BE CONVERTED](http://www.binaryhexconverter.com/binary-to-hex-converter)


A svelte showcase of our CPU's capabilities has been pre-loaded in the cpu.circ file for your convenience and enjoyment, but feel free to write your own programs after carefully reading this documentation. The example program is as follows (in both hex and MIPS assembly format)

- 042: j 2
- 1c0: NOP
- 101: li $r0, 1
- 121: li $r1, 1
- 010: add $r0, $r0, $r1
- 0c1: sw $r0, 1
- 192: beq $r0, $r1, 2
- 1c0: NOP
- 1c0: NOP
- 0c0: sw $r0, 0
- 104: li $r0, 4
- 0a0: lw $r1, 1
- 009: sub $r0, $r0, $r1

*Note:* $r0 and $r1 correspond to our **TWO** registers, 0 and 1.  


Please continue your journey through our CPU and learn its secrets with the following section...

## <p align=center>INSTRUCTION SYNTAX</p>
What follows are the specifications for the three main types of instructions our CPU can handle:
- R-Type: any instruction which deals with multiple registers (e.g. add, sub, etc.)
- I-Type: any instruction involving a single register and an immediate value such as loading immediate values or branching instructions
- J-Type: **JUMP TO ANY INSTRUCTION ADDRESS** (from 0 to 63)

## R-Type

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

### BEHOLD THE R-TYPE FUNC OPTIONS

    0 0 0 – ADD (add the contents of rs and rt, then store result in rd)
    0 0 1 – SUB  (subtract rt from rs and store result in rd)
    0 1 0 – AND (bitwise AND the contents of rs and rt, then store the result in rd)
    0 1 1 – OR (bitwise OR the contents of rs and rt, then store result in rd)

### Example R-Type Instructions

    000 0 0 0 010 - bitwise AND the bits in register 0 with itself and store the result in register 0
    000 1 1 0 000 - ADD register 1 and 0 together and store the sum in register 1
    000 1 0 1 011 - bitwise OR register 1 with register 0 and store the result in register 1


## I-Type

I-Type instructions are denoted by any op-code which is non-zero or the explicit **jump** op-code (001),
and include branching, loading immediate values into registers, and load word from memory.
The structure of an I-Type instruction depends on the on the specific op code used.
For example, an li (load immediate) instruction follows this syntax:

### Load immediate (li)

| op    | rd  | immediate  |
| :---: |:---:| :--------: |
| 1 0 0 |  1  |  1 0 0 1 0 |

(Load immediate value of 18 into register 1)

### Store word (sw)

|  op   | rd  |  address |
| :---: |:---:| :------: |
| 0 1 1 |  0  | 0 1 0 1  |

(Store value at register 0 in memory address 5)

To store a register value in memory (sw in MIPS), use op-code 011. The next bit will be the target register,
and the remaining 5 bits are the address in memory where the value should be stored.

### Branch if equal (beq)

|  op   |   rs  |   rt  |  shamt  |
| :---: | :---: | :---: | :-----: |
| 1 1 0 |   0   |   1   |  1 1 1  |

(If register 0 and register 1 are equal, shift instruction memory 7 instructions ahead)

The only branching instruction currently implemented is beq, which updates the program counter by
a given amount passed in the shamt field.

## JUMP
|  op   | ~~~ destination ~~~   |
| :---: | :-------------------: |
| 0 0 1 |      1 1 1 1 0 1      |

(First Class ticket to instruction 61)

Here it is, the showstopper, the heartbreaker: use this bad boy to jump the program to any instruction that
fits in the immediate field (i.e. 0 to 63)

## NOP
|  op   |  doesn't matter!      |
| :---: | :-------------------: |
| 1 1 1 |  0 0 2 1 6 03333q3333 |

Don't forget about the most important instruction in any computer: No Operation! This charming boy does nothing but let the CPU sit pretty and flex its simulated muscles. All that matters is the op-code (111), so feel free to stuff the rest of the instruction with your favorite bit values (mine's 1), it won't hurt anything, promise!

# That's about it, thanks for reading!
