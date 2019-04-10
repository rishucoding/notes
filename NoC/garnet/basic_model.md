###Garnet###
http://www.gem5.org/Interconnection_Network
Slides by tushar: https://pdfs.semanticscholar.org/c1e9/0beac857ce1af1a531b6538804e71efdef05.pdf
1. NoC is packet switching network embedded on chip. 
> 1. consists of routers, NI and links
> 2. NoC topology defines how the routers, NIs, coherency nodes, and links are organized. 
 

2. A ==router== directs traffic between nodes according to a specified switching method, flow control policy, routing algorithm and buffering policy. 

3. A ==NI== (**Modem**)serves to convert messages between the different protocols used by routersand cores. Also, NI decouples computation from communication, thus allowing things use of both infrastructures independent of each other. 

4. A ==link== is composed of a set of wires and interconnects two routers in the network. 

5. ==Swithching method== defines how data is sent from a src to a dest node. Garnet uses packet switching, where routers communicate through packets or flits(flow control units - atomic units that form packets)

6. ==Flow control policy==: determines how packets move along the NoC and how resources such as buffers and channel bandwidth is allocated. 

7. ==VCs== are often used in flow control: to improve performance by avoiding deadlocks and reducing network congestion. They multiplex a single physical channel over several logically separate channels with individual and independent buffer queues. 

8. ==Deadlock== :  caused by a cyclic dependency between packets in the network, where nodes are waiting to access each other’s resources.

9. ==livelock==: packets do continue to move through the network, but they do not advance to their destination.

10. ==Routing algorithn==: determines for a packet arriving at a router’s input port which output port to forward it to. The output port is selected according to the routing information embodied in the header of a packet.

11. ==Buffering Policy==: defines the number, location and size of buffers, which are used to enqueue packets or flits in the router.

---
### NoC performance parameters: ###

1. ==Average packet latency==: Latency (or delay) is the time elapsed from packet creation at the source node to packet reception at the destination node.

2. ==Throughput==: total amount of received
packets per unit time.

3. Both depends on applied traffic pattern and injection rates. 

4. In garnet, packet latency component consists of packet network latency, which is the packet travel time from source to destination, and the packet queueing latency, which is the sum of packet enqueue time and packet dequeue time.

5. Packet enqueue time represents the time a packet has had to wait at the source node before being injected into the network.

6. Packet dequeue time represents the time a packet has had to wait at the destination node before acknowledgement of its reception.

---
### Garnet Infrastructure: ###

1. a detailed cycle accurate NoC model inside gem5, garnet 2.0 is a 1 stage pipelined NoC model. 

2. allows configurations to model network: 
>1. **number_of_virtual_networks**: This is the maximum number of virtual networks. The actual number of active virtual networks is determined by the protocol.
>2. **control_msg_size:** The size of control messages in bytes. Default is 8. m_data_msg_size in Network.cc is set to the block size in bytes + control_msg_size.
>3. **ni_flit_size**: flit size in bytes. Flits are the granularity at which information is sent from one router to the other. Default is 16 (=> 128 bits). [This default value of 16 results in control messages fitting within 1 flit, and data messages fitting within 5 flits]. Garnet requires the ni_flit_size to be the same as the bandwidth_factor (in network/BasicLink.py) as it does not model variable bandwidth within the network. This can also be set from the command line with --link-width-bits.
>4. **vcs_per_vnet**: number of virtual channels (VC) per virtual network. Default is 4. 
>5. **buffers_per_data_vc**: number of flit-buffers per VC in the data message class. Since data messages occupy 5 flits, this value can lie between 1-5. Default is 4.
>6. **buffers_per_ctrl_vc**: number of flit-buffers per VC in the control message class. Since control messages occupy 1 flit, and a VC can only hold one message at a time, this value has to be 1. Default is 1.
>7.** routing_algorithm:** 0: Weight-based table (default), 1: XY, 2: Custom.: 0: Weight-based table (default), 1: XY, 2: Custom.

3. Garnet2.0 extends gem5’s “ruby” memory system model, which contains parent classes for Topology and Routing.

4. Due to ruby’s memory address range implementation, the number of
directory controllers in a topology is limited to powers of two.

---
### Code base: 
http://www.gem5.org/Garnet2.0
1. ==GarnetNetwork==: class instantiates the main network components: network interfaces (NIs), routers, network links and credit links.

2. A ==network link== can either be an “external link”, which connects a single router to one or more NIs(bidirectional), or an “internal link”, which interconnects a pair of routers.(unidirectional)
>1.they carry flits
>2. Default: a control message is put into a single flit and a data message is 5 flit wide. 


3. A ==credit link== carries VC buffer credits between routers for flow control. 

4. A latency in cycles can be assigned to each router, internal link and external link. 

5. GarnetNetwork calls ==Topology.cc== class which defines the routers, internal links and external links. 

6. Nodes connected through internal links can include cache controllers, directory controllers and DMA controllers.

7. Each network interface connects to one coherence controller. gem5 handles the modeling of CPUs and coherence controllers. Each CPU is connected via its private L1 cache controller.

8. ==Router==: this class houses one routing unit, switch allocator, crossbar switch and for each port one input or output unit.
>1. Buffer write: incoming flit occupying the VCs flit buffers.
>2. Route compute: the routing unit computes the output port for the buffered flit.
>3. VC allocation: the switch allocator arbitrates the input ports by selecting a VC from each input port, in a round robin manner.
>4. Switch allocation: the switch allocator arbitrates the output ports by selecting one input VC as the winner for each output port, in a round robin manner.
>5. Buffer read: the flits that won switch allocation are pulled from their respective input units.
>6. Switch traversal: flits that won switch allocation traverse the crossbar switch.
>7. Link traversal: flits traverse links from the crossbar to their target routers.
> 8. By default, all stages except for link traversal are performed in one cycle. Link traversal occurs in the next cycle.

9. ==Buffer Management: == 
Each router input port has number_of_virtual_networks Vnets, each with vcs_per_vnet VCs. VCs in control Vnets have a depth of buffers_per_ctrl_vc (default = 1) and VCs in data Vnets have a depth of buffers_per_data_vc (default = 4). Credits are used to relay information about free VCs, and number of buffers within each VC.

###Synthetic on-chip network traffic simulations: 
1. GSTI - garnet standalone traffic injector, uses an ISA-agnostic coherence protocol, called Garnet_standalone. 
2. Page : http://www.gem5.org/Garnet_Synthetic_Traffic
3. Summary: The following is a summary of the packet generation procedure of GSTI. Every cycle, each CPU performs a Bernoulli trial with probability equal to the set injection rate to determine whether to generate a packet or not. The injection rate is defined as the number of packets per node per cycle. All randomization is performed deterministically. If the CPU is to generate a new packet, the packet destination will be computed based on the set synthetic traffic pattern. GSTI supports the following synthetic traffic patterns: uniform random, tornado, bit complement, bit reverse, bit rotation, neighbor, shuffle and transpose. Packet destination is embedded into the bits after block offset in the packet address. The packet is randomly tagged as either a read, write or instruction fetch request. The packet is sent to the Garnet_standalone cache controller. The cache controller extracts the destination from the packet address and injects it into the appropriate VNET. Read requests (control protocol) are injected into VNET 0, instruction fetch requests (control protocol) are injected into VNET 1 and write requests (data protocol) are injected into VNET 2, The packet traverses the network and once it has reached the directory controller, it is dropped. The injector terminates after the set amount of simulation cycles has elapsed.

### Deadlock detection: 
The following is a summary of the packet generation procedure of GSTI. Every cycle, each CPU performs a Bernoulli trial with probability equal to the set injection rate to determine whether to generate a packet or not. The injection rate is defined as the number of packets per node per cycle. All randomization is performed deterministically. If the CPU is to generate a new packet, the packet desti-nation will be computed based on the set synthetic traffic pattern. GSTI supports the following synthetic traffic patterns: uniform random, tornado, bit complement, bit reverse, bit rotation, neighbor, shuffle and transpose. Packet destination is embedded into the bits after block offset in the packet address. The packet is randomly tagged as either a read, write or instruction fetch request. The packet is sent to the Garnet_standalone cache controller. The cache controller extracts the destination from the packet address and injects it into the appropriate VNET. Read requests (control protocol) are injected into VNET 0, instruction fetch requests (control protocol) are injected into VNET 1 and write requests (data protocol) are injected into VNET 2, The packet traverses the network and once it has reached the directory controller, it is dropped. The injector terminates after the set amount of simulation cycles has elapsed.

### Ruby random tester
http://www.gem5.org/Ruby_Random_Tester
It is used to test the functional correctness of a  coherence protocol. 
Command 
```bash
./build/X86/gem5.fast ./configs/example/ruby_random_test.py
```