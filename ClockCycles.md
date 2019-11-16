# Clock Cycles 

The terms ‘Clock’, and ‘Cycle’ is likely to be misused throughout this project (sorry). 

Therefore, I'm going to give the 4 main definitions here, to make it easier to figure out which is meant in the context.

The two most important ones are Main Cycle (Mostly called Cycle) and Virtual-Cycle (or VCycle).

Note: these definitions ignore warm-up, spin-up, pre-processing, or other effects that are special cases compared to standard processing.


## Sub-Cycle or Chip Clock Cycle

This is an implementation detail for designs that take more than one Clock Cycle per output. 
Because this is specific to the Fused Multiply Add (FMA) implementation in silicon it's largely ignored in these docs.
This is also the highest frequency rate possible.
It can be generally considered a ratio of Sub-Clocks per Output, like 2:1 would take 2 sub clocks per output value.
The most likely implementation will have this Cycle match the Main Cycle with a 1:1 ratio.


## Main Cycle

Simply, this is the Cycle of a FMA producing one output to the next output. 
The Main Cycle is tied to the cycle of the FMA Units, or the Systolic Array as a whole, any other subsystem would be considered a Secondary Cycle.
This is the main Cycle that is used when describing performance and operation, if the context is not clear, assume this type of Cycle.
If the FMA implemntation produces 1 million outputs per second under full load, then it would have a clock of 1MHz. (over-simplification)
Since, this will define how data and results flow through the device, it's considered the Main Cycle. 


## Secondary Cycle

Some components or sub modules may have to run on a different Clock Cycle due to implementation. 
An example of this would be the speed of the memory subsystem to access RAM.


## Meta-Cycle or Virtual-Cycle (VCycle)

This is the idea that Inputs and Outputs of the VSA is tied to the VDepth value, in a standard ratio, such that a VDepth of 16 will have a ratio of 16:1.
The VSA only pulls in one set of inputs every VDepth Cycles, so, this fact is called a Meta-Cycle, or a Virtual-Cycle (VCycle).
Much of the data flow and processing will be dependent on the VCycle, such as feeding inputs fast enough to avoid starving the VSA.

