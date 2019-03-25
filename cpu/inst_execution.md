### Instruction Execution Principles ###

1. Task that a processor is going to run is represented as a sequence of instructions and the instructions are stored in memory. 

2. Instruction life cycle: all stages of pipeline.

3. Tasks during execution of instruction: 
>1. IF: obtain instruction from program storage. 
>2. ID: determine required actions and instruction size. 
>3. Operand fetch: locate and obtain operand data. 
>4. execute: compute result value or status. 
>5. result store: deposit results in storage for late use. 
>6. next instruction

4. What's inside a generic uP? 
>1. General purpose registers
>2. MAR - memory address registers
>3. MDR - memory data registers
(both interact with the memory)
>4. control logic
>5. PC: contains address of the next instruction to be executed
>6. IR - instruction register