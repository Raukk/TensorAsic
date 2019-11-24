
# Max Pool Proposal

Max pool is a common pooling layer that takes the Max value of a filter from several points along one or more axis.

This can be done by running a 'MAX' or 'Greater Than' on each value for each filter across the 'window'

## Implementation

The basic implementation is to compare the values on a filter wise basis across all the input points in the window.
This can be done with 'N-1' comparisons over SQRT(N) cycles where N is the number of inputs in the 'window' 


### Special Comparator 

Because the values must be MAX'd across a 'window' on a per filter basis, the comparator would need to keep VDepth number of values in memory/buffer.

It would also need to be able to load an input value directly into memory, or store the MAX of input vs the stored value into memory depending on the situation.

It would also need to be able to return the stored max output to the 'next phase'.

Example: for a 1 dimensional Max Pooling of pool size 3; 
1. The VSA Unit outputs the first data point's first filter value which is stored directly in the buffer.
2. The VSA Unit then outputs the next VDepth - 1 Filters (which go to the other buffer slots).
3. The second data point outputs the first filter value which is then compared with the first value from the buffer and then the larger is stored in the buffer.
4. The same action repeats for the next VDepth - 1 Filters again.
5. The final data point outputs the first filter value which is then compared with the large value in the buffer and is outputted to the next stage.
6. The same action repeats for the next VDepth - 1 Filters again.
7. The cycle repeats from the start for the next 3 input long window.

Note: this should work for 1D and 2D Max Pools (I have not investigated 3d Pooling) 
For 2D then pool size Comparators must be compared together to get the final output. 

#### Limitations

Strides != Pool Size are not currently being considered.


## Global Pooling

I haven't really gone through an example, but I think this same code will work fine for Global Pooling, since it's essentially a pool with pool size = input dimensions.






