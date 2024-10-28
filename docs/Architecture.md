### Registers

The processor has four 4-bit general purpose registers: A, B, C and D. It also consists a special purpose register called Instruction Register (IR).

#### IR (Instruction Register)
IR is responsible to hold the current instruction that is just fetched from RAM and is currently being executed. At first CLK signal (T0) of every fetch-decode-execute cycle, the content IR is reset to 0. It can't be used as a general purpose register as there is no instruction that allows loading data into IR or loading data from IR to other register/memory. The Control Unit uses this register to load the instruction from RAM, store it while it is decoded and is being executed.

#### A Register(Accumulator)
The A register can be used as a general purpose register too. However, it is also used by other instructions (STA, LDA and ALU operations) to load or store data.

#### D Register
The D register can be used as a general purpose register too. It is also used by ADD and SUBTRACT operations as the second argument.

#### B and C Registers
The B and C registers are just simple general purpose registers.

Each of the registers is connected to the data bus for input and have an edge triggered LD (load) control signal input. The IR also has an extra RST (reset) signal input.




### ALU

The ALU performs all arithmetic and logical operations in the processor. It consists of 8 basic operations: 
- **Arithmetic Operations**: ADD, SUBTRACT
- **Logical Operations**: NOT, AND, OR, XOR
- **Bitwise Operations**: Left Shift, Right Shift

#### Arguments
ADD and SUBTRACT are binary operations whereas rest are unary operations. For binary operations, the content of A register and D register are taken as arguments (A as minuend for SUBTRACT). For unary operations, the content of A register is taken.

#### Operation Select
To select the one of 8 instructions, a 3 bit switch is used. The lower 3 bits of the OPCODE are taken as the switch bits. The remaining most significant bit has no role in selecting the operation in ALU.

| S3 (Don't Care) | S2  | S1  | S0  | Operation |
| --------------- | --- | --- | --- | --------- |
| 1               | 0   | 0   | 0   | ADD       |
| 1               | 0   | 0   | 1   | SUBTRACT  |
| 1               | 0   | 1   | 0   | COMP      |
| 0               | 0   | 1   | 1   | AND       |
| 1               | 1   | 0   | 0   | OR        |
| 0               | 1   | 0   | 1   | XOR       |
| 1               | 1   | 1   | 0   | LSHIFT    |
| 1               | 1   | 1   | 1   | RSHIFT    |






