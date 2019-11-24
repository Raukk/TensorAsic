# Average Pool Proposal

Average pool is a common pooling layer that averages the values of a filter from several points along one or more axis.

A simple implementation is to accumulate the values for the filter across all the specified points in the 'window' and divide by the number of points.
This is repeated for each filter, and for each window.


## Fuzed Average Pool

The divide by can be fuzed into the next layer by dividing the weights of the next layer by the number points rather than dividing the accumulated value.
This still requires that each value be accumulated together, but removes the scaling factor.

### Special Accumulator

Because the values must be accumulated across a 'window' on a per filter basis, the accumulator would need to keep VDepth number of values in memory/buffer.

It would also need to be able to load it's input value into memory, or load it's output value into memory depending on the situation.

Example: for a 1 dimensional Average Pooling of pool size 3; 
1. The VSA Unit outputs the first data point's first filter value which is stored directly in the buffer
2. The VSA Unit then outputs the next VDepth - 1 Filters (which go to the other buffer slots)
3. The second data point outputs the first filter value which is then added with the first value from the buffer and then stored in the buffer.
4. The same action repeats for the next VDepth - 1 Filters again.
5. The final data point outputs the first filter value which is then added with the total in the buffer and is outputted to the next stage.
6. The same action repeats for the next VDepth - 1 Filters again.
7. The cycle repeats from the start for the next 3 input long window.

Note: this should work for 1D and 2D Average Pools (I have not investigated 3d Pooling) 
For 2D then pool size Accumulators must be added together to get the final output. 

#### Limitaions

Strides != Pool Size are not currently being considered.


## Global Pooling

I haven't really gone through an example, but I think this same code will work fine for Global pooling, since it's esentially a pool with Pool size = Input dimensions.







