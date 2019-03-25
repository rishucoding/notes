#Setting up soc

### TLB/test
```bash
$ mkdir Waveforms
$ python3 test_cam.py
$ gtkwave Waveforms/test_cam.vcd
```


###TLB/src
``` bash
$ make
$ yosys
```
After entering yosys: 
1. yosys> read_verilog Cam.v
(generates RTLIL representation of different modules)
2. yosys> show top
(using show, we can view the the graphiz representation of design by specifying the module  )

###src

1. **AddressEncoder**: 
The purpose of this module is to take in a vector and encode the bits that are one hot into an address. This module combines both nmigen's Encoder and PriorityEncoder and will state whether the input line has a single bit hot, multiple bits hot, or no bits hot. The output line will always have the lowest value address output. 
==Usage==: The output is valid when either single or multiple match is high. Otherwise output is 0.

>Single Bit hot: in a given vector, only 1 bit is high and all other bits are low. eg: 0b100, 0b001, 0b010
> Multiple bit hot: in a given vector, multiple bits can be high. eg: 0b101
> Single match: when single bit hot exists
> Multiple match: when multiple bit hot exists
> output: lowest value address: i think it means that when multiple match exists, like 0b101 : we have 2 hot bits: at 0th position and at 2nd position. So: 0 is output. Another example: 0b010 has single bit hot at position 1 : so ouptut is 1.

###test
1. **assert_op()**: 
> this is used to compare the true value with value obtained from implemented logic. 
>eg: in check_output(dut, o, op) 
>assert_op("Output", out_o, o, op) ; this means compare "o = = op"
>== op = 0 means "====" and op = 1 means "!="
> if the compare is not 1, then it gives an assertion error.
> else it gives "AddressEncoder Unit Test Success" 

2. 
