###Memory consistency and cache coherence


###Chapter 7 : Snooping Coherence Protocols
####Keywords: 
1. unsophisticated three state MSI (baseline) -> protocol optimizations ; new states: Exclusive and Owned states -> higher performance interconnection networks

2. Idea: all coherence controllers observe (snoop) coherence requests in the same order and collectively “do the right thing” to maintain coherence.

3. Traditional snooping protocols broadcast requests to all coherence controllers, (including the controller that initiated the requests) which travel on an ordered broadcast network(eg: bus). Ordered broadcast ensures that every coherence controller observes the same series of coherence requests in the same order, i.e., that there is a total order of coherence requests.

4. 



###Chapter 8 : Directory Coherence Protocols

####Keywords: 

1. Originally developed to address the lack of scalability of snooping protocols. 


