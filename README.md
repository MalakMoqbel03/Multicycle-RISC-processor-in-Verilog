# Multicycle-RISC-processor-in-Verilog
1. Objectives:
To design and verify a simple pipelined RISC processor in Verilog
2. Processor Specifications
1. The instruction size and the word size is 16 bits
2. 8 16-bit general-purpose registers: from R0 to R7.
3. R0 is hardwired to zero. Any attempt to write it will be discarded.
4. 16-bit special purpose register for the program counter (PC)
5. Four instruction types (R-type, I-type, J-type, and S-type).
6. Separate data and instruction memories
7. Byte addressable memory
8. Little endian byte ordering
9. You need to generate the required signals from the ALU to calculate the condition branch outcome
(taken/ not taken). These signals might include zero, carry, overflow, etc.
3. Instruction Types and Formats
As mentioned above, this Instruction Set Architecture (ISA) has four instruction types, namely, R-type, I-type, J-
type, and S-type. These four types have a common opcode field, which determines the specific operation of the
instruction
R-Type (Register Type)
Opcode4 Rd3 Rs13 Rs23 Unused3
Page 2 of 5
• 3-bit Rd: destination register
• 3-bit Rs1: first source register
• 3-bit Rs2: second source register
• 3-bit unused
I-Type (Immediate Type)
Opcode4 m1 Rd3 Rs13 Immediate5
• 3-bit Rd: destination register
• 3-bit Rs1: first source register
• 5-bit immediate: unsigned for logic instructions, and signed otherwise.
• 1-bit mode: this is used with load and branch instructions, such that:
For the load:
0: LBs load byte with zero extension
1: LBu load byte with sign extension
For the branch:
0: compare Rd with Rs1
1: compare Rd with R0
J-Type (Jump Type)
This type includes the following instruction formats. The opcode is used to distinguish each instruction
jmp L # Unconditional jump to the target L.
call F # Call the function F. F is a label.
# The return address is pushed on r15.
The target address is calculated by concatenating the most significant 7-bit of the current PC with the 12-bit
offset after multiplying offset by 2.
Opcode4 Jump Offset12
ret # return from a function.
# the next PC will be the value stored in r7
Opcode4 Unused12
S-Type (Store)
Opcode4 Rs3 Immediate8
This format support only one instruction as flowing
Page 3 of 5
Sv rs, imm # M[rs] = imm
4. Instructions’ Encoding
For simplicity, you are required to implement a subset only of this processor’s ISA. The table below shows the
different instructions you are required to implement. It shows their type, the opcode value, and their meaning
in RTN (Register Transfer Notation). Although the instruction set is reduced, it is still rich enough to write useful
programs.
No. Instr Format Meaning Opcode Value m
1 AND R-Type Reg(Rd) = Reg(Rs1) & Reg(Rs2) 0000
2 ADD R-Type Reg(Rd) = Reg(Rs1) + Reg(Rs2) 0001
3 SUB R-Type Reg(Rd) = Reg(Rs1) - Reg(Rs2) 0010
4 ADDI I-Type Reg(Rd) = Reg(Rs1) + Imm 0011
5 ANDI I-Type Reg(Rd) = Reg(Rs1) + Imm 0100
6 LW I-Type Reg(Rd) = Mem(Reg(Rs1) + Imm) 0101
7 LBu I-Type Reg(Rd) = Mem(Reg(Rs1) + Imm) 0110 0
8 LBs I-Type Reg(Rd) = Mem(Reg(Rs1) + Imm) 0110 1
9 SW I-Type Mem(Reg(Rs1) + Imm) = Reg(Rd) 0111
10 BGT
I-Type if (Reg(Rd) > Reg(Rs1))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1000 0
11 BGTZ
I-Type if (Reg(Rd) > Reg(0))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1000 1
12 BLT
I-Type if (Reg(Rd) < Reg(Rs1))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1001 0
13 BLTZ
I-Type if (Reg(Rd) < Reg(R0))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1001 1
14 BEQ
I-Type if (Reg(Rd) == Reg(Rs1))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1010 0
15 BEQZ
I-Type if (Reg(Rd) == Reg(R0))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2
1010 1
16
BNE
I-Type if (Reg(Rd) != Reg(Rs1))
Next PC = PC + sign_extended (Imm) 1011 0
Page 4 of 5
else PC = PC + 2
17 BNEZ
I-Type if (Reg(Rd) != Reg(Rs1))
Next PC = PC + sign_extended (Imm)
else PC = PC + 2 1011
1
18 JMP J-Type Next PC = {PC[15:10], Immediate} 1100
19 CALL J-Type Next PC = {PC[15:10], Immediate}
PC + 4 is saved on r15
1101
20 RET J-Type Next PC = r7 1110
21 Sv S-Type M[rs] = imm 1111
5. RTL Design
You are required to design a 5-stage pipelined processor (fetch, decode, ALU, memory access, and write back).
The design should include a datapath and control path that support all of the aforementioned instructions.
