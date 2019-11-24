
# Rectified Linear Unit Hardware Implementation Proposal 

ReLU operates as a simple MAX(value, 0.0).

## Implementation

ReLU is very easy to implement in Hardware, since if the number is positive-zero or greater, then the value is used, otherwise zero is used.

In BFloat16, the first bit is the sign bit, and if the sign bit indicates negative then simply return all zeros, otherwise return the value.
Note: Positive zero is all zeros `0 00000000 0000000'

An additional feature is a configurable bit that controls if the ReLU unit is Activated or Deactivated, where it always passes through the value if it's deactivated.

This should allow the ReLU hardware to be placed judiciously but only be applied where the compiler has configured it.

Over all the hardware implementation of ReLU should require only a few transistors.
For BF16, it would require the config buffer bit, control system, the signal lines, and then 16 AND gates (one for each bit of the input).







