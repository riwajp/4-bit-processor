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
![[ALU.png]]
The ALU performs all arithmetic and logical operations in the processor. The output of the operations performed by the ALU is set to Accumulator (A Register). It consists of 8 basic operations: 
- **Arithmetic Operations**: ADD, SUBTRACT
- **Logical Operations**: NOT, AND, OR, XOR
- **Bitwise Operations**: Left Shift, Right Shift

#### Arguments
ADD and SUBTRACT are binary operations whereas rest are unary operations. For binary operations, the content of A register and D register are taken as arguments (A as minuend for SUBTRACT). For unary operations, the content of A register is taken.

#### Operation Select
To select the one of 8 instructions, a 3 bit switch is used. The lower 3 bits of the OPCODE are taken as the switch bits. The remaining most significant bit has no role in selecting the operation in ALU.

| S3  | S2  | S1  | S0  | Operation |
| --- | --- | --- | --- | --------- |
| 1   | 0   | 0   | 0   | ADD       |
| 1   | 0   | 0   | 1   | SUBTRACT  |
| 1   | 0   | 1   | 0   | COMP      |
| 0   | 0   | 1   | 1   | AND       |
| 1   | 1   | 0   | 0   | OR        |
| 0   | 1   | 0   | 1   | XOR       |
| 1   | 1   | 1   | 0   | LSHIFT    |
| 1   | 1   | 1   | 1   | RSHIFT    |




### System Bus
![[SystemBus.png]]
The system bus consists of a 4-bit Data Bus, an 8-bit Address Bus and some control signals. 

#### Data Bus
The processor contains a 4-bit data bus.  The content on the data bus is selected from among the registers and the memory, by a  series of multiplexers. The table below shows the control signals and the corresponding content of data bus when that signal is enabled. 

| Control Signal | Data Bus Content               |
| -------------- | ------------------------------ |
| ALU_OP         | Output from ALU                |
| ACC_SELECT     | Content from Accumulator       |
| MEM_SELECT     | Content from Memory            |
| PC_EN          | Content from Memory            |
| Otherwise      | Content from selected Register |

##### Selecting Registers
For operations related to data movement involving the registers, the lower 4 bits (L) of the address bus is used along with the opcode in the instruction. The lower 2 bits of L are used to generate signals that selects one of 4 registers to load their content into data bus. The higher 2 bits of L select the LD signals for one of 4 registers. The load signals are labelled as REG1 to REG4, each signal acting as LD signal for one of the registers. The LD signal enables the destination register to load the content of data bus into it.

#### Address Bus
The processor contains an 8-bit address bus, divided into 2 halves. The lower order 4 bits are held by L and higher order 4 bits by H i.e. address are formed as : HL. The two halves HL are relevant only in the context of loading the address using instructions directly, i.e. when the value of PC (Program Counter) is loaded into address bus, the concept of HL is irrelevant, it is loaded as a single 8-bit address.

The address bus can hold one of 2 addresses:
1. The address loaded using instructions i.e. HL.
2. The PC address.

The address held by the address bus is selected by the PC_EN control signal. If PC_EN is high, the address bus is set to PC value, else HL value.








