### T-CREST  Memory Network-on-chip

**keyword:** WCET (worst case execution time)

1. T-CREST multi core setup looks like this: 
![setup](images/Selection_017.jpg  "setup")

2. Two NoCs in T-Crest: 
>1 . core to core
>>1. message passing between cores
>>2. most traffic shall stay on-chip
>>3. torous topology used
>>4. TDM based arbitration

>2 .  cores to memory: 
>>1. read and write (cache line) burst
>>2. shared memory
>>3. tree architecture
>>4. TDM based arbitration

___
**Paper: **
AIM: Time predictable memory arbitration and access for CMPs. 

*Keywords: * WCET, RTS, time-predictable compArch, memory arbitration, timing compositional, 

1. To derive safe bounds on worst-case execution times (WCETs), all components of a computersystem need to be time-predictable: the processor pipeline, the caches, the memory controller,and memory arbitration on a multicore processor.

2. Memory NoC is organized a  tree with TDM of accesses to the shared memory. 

3. **How many cores can one fit into one chip? **
>1. maximum cores till now? 
>2. trend while increasing number of cores! 

4. core to core NoC is used for message passing ; memory NoC is used to connnect all processor cores to the shared external memory via the memory controller. 

5. paper uses TDM scheduling from end to end, such that read or write transactions towards the shared memory are transmitted from the initiating core to the memory ==*without any dynamic arbitration or buffering.*==

6. **Crux: ** 
>1. only processor-local memories buffer any data. 
>2. transactions are injected according to a global schedule, and thus propogated in a pipelined fashion even ==without flowcontrol==
>3. TDM slots and TDM schedule are defined by the sequence of equally sized read or write transactions towards the memory. 
>4. this results in a simple hardware implementation and a straightforward WCET analysis. 
>5. Actually, we are having a global TDM schedule distributed at the processor cores results in a distributed arbitration .. which is good.. as it scales well with increased no. of cores. 
>6. There is pipeline in the design, and pipleline dealys contribute to the timing analysis. 
>7. there is no buffering in the NoC and in the memory controller. 

7. What does it mean by timing compositional architecture? .. this means that the arch has no timing anomalies or unbounded timing effects. 