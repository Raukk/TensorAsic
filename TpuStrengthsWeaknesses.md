# TPU
This is a high level evaluation of Google's TPU based on the limited information that has been made public and my understanding of it.


## TPU Information 
Most of the information on Google's TPU comes from the information provided about the V1 version and may not directly translate to V2, V3, Edge, or future iterations.
https://cloud.google.com/blog/products/gcp/an-in-depth-look-at-googles-first-tensor-processing-unit-tpu
But some information is availible on the newer designs, like:
https://cloud.google.com/blog/products/ai-machine-learning/what-makes-tpus-fine-tuned-for-deep-learning


### A note on Deep NN performance
Every Deep Net is either Compute Limited or Memory Limited. While there exist a veriety of methods to deal with Compute Limited networks, there are no well known methods to improve memory limited networks. 


## Systolic Array
The TPU is based arround the idea of a Systolic Array https://en.wikipedia.org/wiki/Systolic_array
While the V1 used a 256x256 systolic array of int8 MAC units, the V2 and V3 use 128x128 systolic array of float MAC units.
For most of the comparison I will use the 128x128 because V1's int8 requires that the model already be trained and quantitized. 


### Strengths of Systolic Arrays
The main benifit of the systolic array is that once the weight values have been set, they no longer require any bandwidth until they change.
This is very significant because for the 128x128 array, it stores 16,384 weights, and can process just 128 input values every cycle while still doing 16,384 MACCs per cycle.
This high throughput and reduction in bandwdth is what makes them so good for DNNs.
By running at a high clock cycle and turing 128 inputs into 128 outputs, a systolic array is able to crunch huge amounts of data at blazing fast speeds.

### Weaknesses of Systolic Arrays
However, Systolic Arrays are still able to be memory limited. The V1 used more of the chip area for 'cache' than it did for the systolic array, resulting in a huge 24MB 'cache'
Why this was needed can be demonstradted by looking at a deep layer in the MobileNetsV2 implemntation, a 1x1 convolution with 160 inputs and 960 outputs.
Because the Systolic Array can only take 128x128 bites out of any layer, it must essentially be tiled into 128x128 tiles, giving 2x8 tiles (256x1024)
Notice that half the tiles are only using 1/4 of the computational power with only 32 inputs (160 - 128) and the last corner tile is only 32x64 which used only 1/8th the computational power
This results in a total utilization just above 50% (very rough figure)
Looking at the last tile, it's possible to add in some other small layer or partial layer, but it still results in a major loss of computations.
For example, running two 64x64 will result in 1/2 utilization, and using four 32x32 will result in 1/4 utilization, and eight 16x16 will give 1/8th utilization.


The result of this is that to complete the calcualtions of just this one layer, the weights must be set 16 times for a total of 262,144 weights.
Depending on the implementation, setting the weights could take 128 clock cycles or more per tile, which would total 2048 for all 16 tiles.
Lets assume that the dimensions have been reduced significantly, down to just 7x7 points, or 49 datapoints. 
It would be pretty costly to only process 49 inputs for each time we set the weights, so it's likely to utilize batches (at least in training).
Lets assume we use a very small batch size of 50, we now process 2450 inputs for each time we set the weights.
That seems like a great improvement, and it is, but now, we must not only processes these values, but store the outputs for the next layer, which is wehere we get to the huge 'cache'
Each datapoint outputs 960 values, which we can multiply by our 2450 inputs to get a meager 2,352,000 outputs.
In the V1, each of those outputs would be a int8, and could take up 10% of the 24MB 'cache' or would require being moved to other storage, such as RAM.
If a larger image, larger Batch size, or a larger layer were used, it could easily expand beyond what could be stored in cache and force the storage to RAM, And this is where the Memory Limited comes in.
If the TPU can keep everything inside cache, and feed all layers from begining to end with very few memory accesses, it can be very fast.



