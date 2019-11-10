# Possible Variations of VSA units

The Reference Implementation is likely to use the simplest design possible, but there are a lot of other options that could be implemented.

This document identifies some of the variations that could be created.

Pretty much all these options trade added complexity for improved performance. 

Commercial Companies may wish to implement these ideas to get the extra performance, since they can pay the complexity cost.


## 2D Virtual Units

A simple expansion of the Virtual Unit Idea to be Virtual in Width and Depth.

By storing several inputs in looping memory, a single unit can have a Virtual Width as if it was M Units wide and N Units deep.
Note: It must be able to store NxM Weights in the unit's loop, this tends to require a very large weights loop.

### My Take: 

For the majority of cases, this added complexity give very little benefit.


## Variable VDepth Units

It would be simple to define a large weight loop VDepth that can be 'short-circuited' to act as a shorter loop (lower VDepth).

This would require that it be able to consume more inputs per unit, since it would require one input value per loop, but the loop could be reduced (potentially to VDepth = 1).

### My Take: 

I originally intended the Reference design to use this, but the variation in Input feeding seemed like it'd be more complicated. The changes to the actual VSA Unit are pretty simple, but the data infeed needs to be considered.


## Input fall through

A Standard Systolic Array uses input fall through to partially reuse the input values. 
The default VSA Unit keeps the current input in the Unit until it's done for partial reuse.

However, the VSA Unit can only process depths up to VDepth, if a greater depth is needed, the inputs must be duplicated.

By allowing the VSA Unit to also let values fall through to the next Unit, it can pass Inputs without them being explicitly duplicated.

However, this creates some additional challenges;
if no alternate input path is provided to the fall through unit, then when lower depths are needed, the units sits idle.
If an alternate input path is defined for the fall through unit, then this alternate path results in added complexity.
The same functionality can be approximated with input duplication.

Note: In some edge cases + implementations, using fall through can reduce the latency. But it would be comparable to Input Duplication.

### My Take:

If combined with Variable VDepth, then using fall through is more beneficial, but without Variable VDepth, only a small number of fall throughs are practical.
For Example, a VDepth=32 network with 4 fall throughs would have a true VDepth of 128 (same as TPU) but without Variable depth, would loose performance on any layer with less than 128 outputs.


## Include Bias in the Units

Since each Unit Accumulates Values from the previous Unit, the first Unit could receive the Bias as an input for the Accumulator.
The biases would be stored in a looping buffer the same as the weights.

### My Take:

This adds additional complexity, and if you check the Compiler Tricks Document, it's possible to use an extra Unit to approximate this. 
Additionally, because external accumulators are going to be needed, those could (probably) take care of the Bias easily.






