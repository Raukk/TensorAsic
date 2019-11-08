# TPU

This is a high level evaluation of Google's TPU based on the limited information that has been made public and my understanding of it.
Note: most statements here are based on a naive Systolic Array Implementation, and not specifically on the Google implementation.


## TPU Information 

Most of the information on Google's TPU comes from the information provided about the V1 version and may not directly translate to V2, V3, Edge, or future iterations.
https://cloud.google.com/blog/products/gcp/an-in-depth-look-at-googles-first-tensor-processing-unit-tpu
But some information is available on the newer designs, like:
https://cloud.google.com/blog/products/ai-machine-learning/what-makes-tpus-fine-tuned-for-deep-learning


### A note on Deep NN performance

Every Deep Net is either Compute Limited or Memory Limited. While there exists a variety of methods to deal with Compute Limited networks, there are no well known methods to improve memory limited networks. 


## Systolic Array

The TPU is based around the idea of a Systolic Array https://en.wikipedia.org/wiki/Systolic_array
While the V1 used a 256x256 systolic array of int8 MAC units, the V2 and V3 use 128x128 systolic array of float MAC units.
For most of the comparison I will use the 128x128 because V1's int8 requires that the model already be trained and quantized. 


### Strengths of Systolic Arrays

The main benefit of the systolic array is that once the weight values have been set, they no longer require any bandwidth until they change.

This is very significant because for the 128x128 array, it stores 16,384 weights, and can process 128 input values every cycle while still doing 16,384 MACCs per cycle.

This high throughput and reduction in bandwidth is what makes them so good for DNNs.

By running at a high clock cycle and turing 128 inputs into 128 outputs, a systolic array is able to crunch huge amounts of data at blazing fast speeds.


### Weaknesses of Systolic Arrays

Systolic Arrays are still able to be memory limited. 
If one was feed directly from a  PCIE 4.0 x16 slot, it would have a Input bandwidth of 31.51 GB/s if saturated. A Systolic Array of 32-bit floats (4 bytes each) that is 32 wide by 32 deep, would saturate the input at around 250 Mhz clock (250,000,000*32*4 ~= 30 GB/s).

This is why simply plugging a Systolic Array into an existing chip, architecture, or into a motherboard slot does not give amazing results.

The V1 runs at 700Mhz, but it also used more of the chip area for 'cache' (Unified Buffer) than it did for the Systolic Array, resulting in a huge 24MB 'cache'.

Why this was needed can be demonstrated by looking at a deep layer in the MobileNetsV2 implementation, a 1x1 convolution with 160 inputs and 960 outputs.
Because the Systolic Array can only take 128x128 size chunks out of any layer, our 160x960 must be tiled into 2x8 tiles (each 128x128) for an effective size of 256x1024.
Notice that half the tiles are only using 1/4 of the computational power with only 32 inputs (160 minus 128) and the last corner tile is only running 32x64 which used only 1/8th the computational power*.
This results in a total MACC utilization around 60% 

Another result of this is that to complete the calculations of just this one layer, the weights must be (re)set 16 times for the total of 262,144 weights (zero padded).
Depending on the implementation, setting the weights could take 128 clock cycles or more per tile (it could take zero clocks), which would total 2048 for all 16 tiles. Note: I'm not sure how many clocks Google's TPUs take to set weights.

If the architecture doesn't let the weights be set faster, then we need to utilize batch size to avoid spending all our time setting eights. 
For example, let's assume that the x,y dimensions have been reduced significantly, down to just 7x7 points (this is the value for the standard Mobilenets V2). 
It would be pretty costly to only process 49 inputs for each time we set the weights since it'd take 49 cycles to calculate, and 128 cycles to set weights, for an efficiency of aproximatly 25%. 
That's 25% on top of the 60% for a stunning ~15% utilization, so it's likely to utilize batches (at least in training).

Even with modest batch sizes, like 10, the utilization is no longer a problem (up to 80% from 25%) but begins to cause another problem.
Let's take the batch size of 128 that Google recommends (per core), and now we process 128 items, with 49 data points, with 160 input values each, and 960 output values each.
These numbers give us a million input values, and 6 million outputs, before zero-padding, so, using 32-bit floats we feed in ~6.2MB of input values per batch, and get out ~24.5MB of outputs per batch.

Wait, 24.5MB... that's more than the 'Cache' on the V1, which means the outputs of this Layer's batch must partially or fully be sent to bulk storage, in the case of the V1 it's DRAM.

Ok, that doesn't sound too bad, right? 
The DRAM on the V1 still has 30GiB/s bandwidth, but that's way worse than the 167GiB/s that the 'cache' has. 

Note: the V1 uses int8 and therefore would output 1/4 the memory, but small changes like larger image size, bigger batches, or more outputs would all push the memory output higher. 

Basically, as long as the TPU can keep everything in the super fast 'cache' on chip, it will be able to crunch numbers with scary high throughput.
But as soon as it can't the memory bottleneck shows up to ruin the utilization.


#### Grouped Convolutions

Finally, let's look at Grouped convolutions, and then the special case of depthwise layers.

For simplicity, let's assume we have a layer taking 128 inputs and giving 128 outputs running on a 128x128 Array. 
This is basically the perfect case, where it reaches 100% throughput and is awesome.
But, what if we decided to split the layer into two 64 input and 64 output layers? This should reduce the number of weights and multiplications required by 1/2 on most hardware. 

But not on a Systolic Array, because it means it must run two separate sets of weights at 25% utilization each, that makes it worse than the non-split implementation. But there's a trick! Using weight zero padding both sets of weights can be used by putting them at a diagonal. But, two 64x64 still take the same as one 128x128, just without utilizing as much math.

This approach scales fairly simply,for example, running two 64x64 will result in 1/2 utilization, and using four 32x32 will result in 1/4 utilization, and eight 16x16 will give 1/8th utilization.

###### Depthwise Separable Convolutions

Depthwise are a special case of Grouped Convolution because instead of having 2 groups like our example, we have N groups where N is the number of input filters.

For MobileNets V2 a 3x3 depthwise convolution is used, so, 9 values of each input filter are taken and multiplied by 9 weights, where the sum is the output for that filter.

If we take the same as before, then we end up with a 1x9 set of weights for each of the 960 input filters. If we did the naive approach, it would take the Systolic array 960 different sets of weights (at less than 0.1% utilization), but as we mentioned we can set them diagonally, then we fit 14 per set of weights (9x14=126) so we only need to reset the weights 69 times.
Even running 14 at once results in less than 1% utilization ((14x9)/(128x128)) and takes more weight resets than the expansion layer or pointwise layer. 

