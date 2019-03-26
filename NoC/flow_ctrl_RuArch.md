### Flow control and Routing microarchitecture ###

#### Keywords ####

1. **Question: ** When two packets trying to use the same link at the same time? *Handling the contention?* 
*choices* : buffer, drop, misroute the packet not being serviced!  

2. packets are buffered in the router till it gets a productive output port! 
> 2 cases to buffer: 
>>1. not getting the productive port due to conflict with other packets.
>>2.  due to lack of buffer availability at the next router. 

3. upstream router: router from which packet originates.

4. downstream router: routers in the path of packet transmission

5. Upstream router show know the buffer availability of downstream router. How? credits should be exchanged between routers by handshake signals. 

6. This is flow control: via this the upstream router gets to know whether there is a buffer available at the downstream router (this is done before forwarding the packet).

7. Every router has a set of buffers at its input; flow control ensures smooth flow of packets and facilitated by a proper handshaking mechansim between adjacent routers. 

8. Store and forward: packet based flow control: packet is copied entirely into router before moving to next node. Packets' forward progress is blocked if flit TAIL hasn't reached the upstream router when downstream router has free buffer availability. (missed the opportuniny)therefore causing high latency. It requires buffering for entire packet at each node.(costly) 

9. Virtual cut through switching: Make forwarding as soon as header is received and resources (buffer, channel, etc) are allocated

10. **Wormhole flow control**: packet broken into flits: flits are sent across the fabric in a wormhole fashion  
>1. body follows head flit, and tail flit follows body flit. 
>2. pipelined
>3. if head blocked, rest of the packets blocked. 
>4. routing information (src/dest) is only stored in head. 
>5. proves to be having lower latency and efficient buffer utilization. 
>6. packet occupies resources across multiple routers. 
>7. duty of tail flit is to deallocate the resoures. 

11. Head of line blocking problem: 
>1. if head flit, cannot move due to contention, another worm can't proceed even though the links may be empty. 

12. Virtual Channel Flow control: 
>1. multiplex multiple channels over one physical channel. 
>2. FIFO buffers are replaced with multilane buffers: divide up the input buffer into multiple buffers sharing a single physical channel. 
>3. So instead of having single queue structure, have a parallel structure. 
>4. VCs are allocated once at each router to the head flit and remaining flits of the packet inherit the same VC. 
>5. Flits of different packets can be interleaved on same physical channel. 
>6. Pool of buffers are useful in avoiding deadlocks. 

13. **Structure of Router:**
>1. input port with buffers (MUXed VCs).
>2. crossbars: going to connect the inputs to the corresponding outputs.
>3. input ouput data movement is faciliated by a Control logic block

14. Functions of a router: 
>1. buffering of flits. 
>2. route computation: process of finding the ouput port for an incoming packet (HEAD flit) .
>3. VC allocation : the process of reserving a buffer in the downstream router 
As flow control is based on handshaking between adjacent routers : as in if an injected packet wants to go to North router, then north router will give feedback that a buffer is empty. 
>4. switch allocation: when multiple flits are competing for the same output port: which of the flit has to be chosen (arbitration)
>5. switch traversal: finally, flits are going to traverse by the switch (max 5 flits can traverse at the same time:: 5 input ports and 5 ouptut ports)
These operations happen inside the router. After this *Line Traversal* happens. 

14. **Router pipeline:**
>1. BW(buffer write) -> RC (route computation) -> VA (virtual channel allocation) -> SA (switch allocation) -> ST (switch traversal) 
>2. Similar to instruction pipeline, i  can parallely work here. 


15. Wormhole Router Pipeline: 
>1. route computation performed once per packet. 
>2. VC allocated once per packet. 
>3. body and tail flits inherit this information from the head flit. 
Question: Why switch allocation is done per flit? Because every flit is allocated an output port. Even though contiguous flits will take the same output port, there can be other competing flits of other packet trying for the same port. So, we need to arbitrate between contesting flits. .! 

16. Dependencies in a router: 
>1. one task can be only done if some other task is over. Like : SA is done after VA. So, we have dependence between output of one module and input of another. 
>2. Determine critical path through router. 
>3. note: cannot bid for switch port until routing computation is done. 
>4. A flit spends a total of (each individual state time ) ka sum.

17. Lookahead routing: **???** 
>1. Current router can perform route computation for the next router. 
>2. Precomputing route allows flits to compete for VCs immediately after BW

18. Speculative routing: **???**

19. **Selection Strategy:**
>1. *Question:* When there are multiple paths for a packet at a router, which one to choose? 
>2. An \*Adaptive Routing Function\* will return a set of possible ouptut channels.
>3. Congestion Feedback is collected from neighbours, and based on that a channel is selected. 
>4. Cache miss / coherence packets need to be serviced fast by reducing latency of packets. Congestion increases average latency of packets. Selection strategy chooses path with less congestion -> thus reducing average latency of packets. 

20. **Input channel selction:**
>1. Let's say that three input port flits want to travel through N output port. Which one to select? 

21. **Output channel selection**
>1. Let's say that one packet has options to go to destination via N port or S port. Which port to choose? 

22. Switch level packet scheduling: 
>1. In a router: among all the competing flits in buffer, which flits are going to be scheduled to 5 output ports? 



 


