# Planet Evo

Evolution simulation software

![](http://i.imgur.com/ts7HkqZ.png)

## DNA & Reproduction

In this project, DNA is an array of instructions. Each instruction
is 2 bytes. When an instruction is interpreted, if the 16-bit integer
value is out of range, it is wrapped around (using modulus for example)
as many times as necessary until a value in range is found.

Each cell (particle) of an organism has a complete intact listing of its
DNA, as well as a "currently executing" DNA. There is a pointer to the current
index in the currently executing DNA which henceforth will be named "PC".
The "currently executing" DNA may be modified in place during the course of 
DNA execution.

When cell division occurs, the original cell is left intact, and a new cell
forms in one of the cardinal or diagonal directions. The new cell inherits
the "currently executing" DNA along with the PC as well as the intact listing.
Now both cells will continue executing their now independent DNA programs
at the same position. Some state is different for each cell, however.
For example, cell age is 0 for the new cell and nonzero for the old cell.

When cell death occurs, all stored energy is released, and the cell stops
executing DNA code.

There are 2 registers, X and Y. Instead of parameters being data following the
instructions, all parameters are global variables and each have their own
instruction to set.

All instructions are 2 bytes each. Since all instructions are the same size,
mutations can be more easily and predictably applied on an
instruction-by-instruction basis.

### Core Instructions

* 0 - Perform cell division. Parameters:
  - CellDivisionEnergyForNewCell(ValueSource)
  - CellDivisionDirection(Direction8)
  - CellDivisionNewCellType(NewCellType)
  - CellDivisionForkLabel(CodeLabel)
  - CellDivisionContingencyPlan(BlockOrContinue)
* 1 - Perform cell death.
* 2 - Jump. Parameters:
  - JumpOperandLeft(ValueSource)
  - JumpOperandRight(ValueSource)
  - JumpComparison(Comparison)
  - JumpLabel(CodeLabel)
* 3 - Noop.
* 4 - Wait. Parameters:
  - WaitSource(ValueSource)
* 5 - Update a register with a value. Parameters:
  - UpdateRegisterSource(ValueSource)
  - UpdateRegisterDest(Register)
* 6 - Perform a calculation. Parameters:
  - CalcOperandLeft(ValueSource)
  - CalcOperandRight(ValueSource)
  - CalcOperation(Operation)
  - CalcDest(Register)
* 7 - Modify DNA. Parameters:
  - ModifyDnaLabel(CodeLabel)
  - ModifyDnaSource(ValueSource)
* 8 - Skip next instruction.

### Parameter Setting Instructions

* 9 - CellDivisionEnergyForNewCell - default 5
* A - CellDivisionDirection - default up
* B - CellDivisionNewCellType - default none
* C - CellDivisionForkLabel - default 0
* D - CellDivisionContingencyPlan - default block
* E - JumpOperandLeft - default Register X
* F - JumpOperandRight - default Register X
* G - JumpComparison - default left operand is non-zero
* H - JumpLabel - default 0 (no jump - continue on next instruction)
* I - WaitSource - default value 2
* J - UpdateRegisterSource - default none
* K - UpdateRegisterDest - default none
* L - CalcOperandLeft - default none
* M - CalcOperandRight - default none
* N - CalcOperation - default left operand
* O - CalcDest - default none
* P - ModifyDnaLabel - default 1
* Q - ModifyDnaSource - default register X
* R - ValueSourceDirection - default up
* S - ValueSourceNumber - default 0
* T - ValueSourceLabel - default 1

### Parameter Values

All parameter values have a size of 256. If a value exceeds the bounds, it wraps.

#### Register

* 0 - none - this operation is a noop
* 1 - register X
* 2 - register Y

#### ValueSource

* 0 - None. This operation is a noop.
* 1 - Register X.
* 2 - Register Y.
* 3 - Cell age.
* 4 - Organism age.
* 5 - The value 0.
* 6 - The value 1.
* 7 - The value 2.
* 8 - Type of particle to the ValueSourceDirection(Direction8).
* 9 - Count of cells in organism.
* A - Cell energy value.
* B - The value 3.
* C - The value 4.
* D - The value 5.
* E - Boolean whether particle to ValueSourceDirection(Direction8) is same organism.
* F - Boolean whether particle to ValueSourceDirection(Direction8) is same cell type.
* G - ValueSourceNumber(Number)
* H - ValueSourceLabel(CodeLabel)
* I - My own cell type.

#### Comparison

* 0 - Never
* 1 - Always
* 2 - Left operand is zero
* 3 - Left operand greater than right operand
* 4 - Left operand less than right operand
* 5 - Left operand equal right operand
* 6 - Left operand greator or equal to right operand
* 7 - Left operand less or equal to right operand
* 8 - Right operand is non-zero
* 9 - Right operand is zero
* A - Left operand is non-zero

#### Operation

* 0 - left operand
* 1 - 1
* 2 - 2
* 3 - 0
* 4 - left operand + 1
* 5 - left operand + 2
* 6 - right operand
* 7 - right operand + 1
* 8 - right operand + 2
* 9 - left operand + right operand
* A - left operand - right operand
* B - right operand - left operand
* C - -left operand - right operand
* D - left operand / right operand
* E - right operand / left operand
* F - left operand * 2
* G - right operand * 2
* H - left operand mod right operand
* I - right operand mod left operand
* J - return which is smaller: left or right operand
* K - return which is larger: left or right operand
* L - left operand * right operand

#### CodeLabel

Number of bytes offset [0, 255]. When a mutation occurs, this number
is adjusted to point to the same location.

#### Direction8

* 0 - up
* 1 - down
* 2 - left
* 3 - right
* 4 - top/left
* 5 - top/right
* 6 - bottom/left
* 7 - bottom/right

#### Direction2

* 0 - positive
* 1 - negative

#### NewCellType

* 0 - None - this instruction is a noop
* 1 - Chloro
* 2 - Fiber

#### BlockOrContinue

* 0 - Block - stay on this instruction until conditions are satisfied
* 1 - Continue - ignore this instruction if conditions are not satisfied

#### Number

Integer from [0, 255]
