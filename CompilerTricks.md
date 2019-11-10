# Compiler Tricks*

This document is intended to define certain tricks that can be implemented in the compiler (or in the hardware) that improve performance or add extra features.


## Bias as a Weight

If the value in an input column is `1.0`, then by multiplying a weight by it results in the weight value. 
By setting the weights for one Unit (column) as the bias values, and then setting all the inputs to that column as 1.0 it will accumulate the Bias value onto the results sum.

While the standard accumulators may take care of this, it's a simple way 


## Average Pooling

Average pooling can be implemented as a simple set of weights set on the Units, but is not very efficient depending on the implementation.
For a 2x2 Average Pooling Implementation using 4 Units with weights set to 0.25 would result in the accumulation being the Average.

But, for VDepth greater than 1, the extra calculations get wasted, since the inputs should not be reused.








