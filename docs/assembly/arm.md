# Arm architecture
## Arm registers

| Name | Alias | Purpose |
|:----:|:----:|:------|
| R0 |	–	|  General purpose |
| R1 |	–	|  General purpose |
| R2 |	–	|  General purpose |
| R3 |	–	|  General purpose |
| R4 |	–	|  General purpose |
| R5 |	–	|  General purpose |
| R6 |	–	|  General purpose |
| R7 |	–	| Holds Syscall Number |
| R8 |	–	|  General purpose |
| R9 |	–	|  General purpose |
| R10 |	– | General purpose |
| R11 | FP | Frame Pointer |

**Special Purpose Registers**

| Name | Alias | Purpose |
|:----:|:----:|:------|
| R12 | IP |	Intra Procedural Call |
| R13 |	SP |	Stack Pointer |
| R14 |	LR |	Link Register |
| R15 | PC |	Program Counter |
| CPSR | – |	Current Program Status Register |

The `CPSR`  contains the following ALU status flags:

- `N`: Set when the result of the operation was Negative.
- `Z`: Set when the result of the operation was Zero.
- `C`: Set when the operation resulted in a Carry.
- `V`: Set when the operation caused overflow.

!!! note 
    PC points two instructions ahead of the current PC value, because older ARM processors always fetched two instructions ahead of the currently executed instructions

## Most common instructions
- `MOV`	- Move data	
- `EOR`	- Bitwise XOR
- `MVN`	- Move and negate	
- `LDR`	- Load
- `ADD`	- Addition	
- `STR`	- Store
- `SUB`	- Subtraction	
- `LDM`	- Load Multiple
- `MUL`	- Multiplication	
- `STM`	- Store Multiple
- `LSL`	- Logical Shift Left	
- `PUSH` - Push on Stack
- `LSR`	- Logical Shift Right	
- `POP`	- Pop off Stack
- `ASR`	- Arithmetic Shift Right	
- `B`	- Branch
- `ROR`	- Rotate Right	
- `BL`	- Branch with Link
- `CMP`	- Compare BX	
- `Branch` - and eXchange
- `AND`	- Bitwise AND	
- `BLX`	- Branch with Link and eXchange
- `ORR`- Bitwise OR	
- `SWI/SVC`	- System Call