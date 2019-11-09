# How to solve the issues with Systolic Arrays

I outlined in the TpuStrengthsWeaknesses document what there are weaknesses in it's design and implementation.
Here is my solutions to the Systolic Array's weaknesses. 


### Quick notes on my terminology

I visualize a Systolic Array taking inputs at the top and pushing outputs out either the right or left side. Some Systolic Array  visualizations may take the inputs from another side (left/right or bottom).

Because of this I define "fall through" as an input going from one Systolic Array Unit to the next as it moves from the top to the bottom of the Systolic Array.

I call this "partial reuse" because the same value is reused up to N times, where N is (normally) the depth of the Systolic Array.
"Partial reuse" primarily applies to the values Inputted into the Systolic Array. 

I call the weights "full reuse" because the weights will be saved until new weights are assigned, giving unlimited reuse.
"Full reuse" primarily applies to the weights into the Systolic Array. 


## Systolic Array Benefits

The main benefits of a Systolic Array is because it leverages full reuse and partial reuse to limit the memory usage. 
This reuse limites the memory access and clock cycles needed while still allowing a large number of computations to happen.

Because of fall through, the inputs are partially reused. The weights are fully reused once they are set.

This allows the massive parallel compute of the Systolic Array to not be memory limited like it would be on a CPU or even GPU.


## The "Virtual Systolic Array" (VSA)

Name Note: This is probably a bad name for it, but I don't have anything better at the moment.

What's the difference between a Systolic Array and a "Virtual Systolic Array"?

A Systolic Array Unit stores only one weight value which is multiplied by a new input each cycle, as the Inputs fall through one Unit to the next every cycle.

Because Systolic Array Units are arranged in a grid, they can do NxN (assuming a square Array) MACCs per cycle. 

A Virtual Systolic Array Unit stores N weights (instead of one). It stores them in a circular shift register (or similar).

Because the weights are stored in a circular manner that repeats every N cycles, we don't have to reset them unless we are changing all the weights, so it keeps its "Full reuse" characteristic.

In the generic case, the Input is only changed every N cycles to match the weights looping (a special case involves it being more often).

In this case, a single VSA Unit acts as a whole column of a standard Systolic Array, but only calculates one multiplication per cycle.

I use the term "Virtual Depth" or "VDepth" to describe N in this case.


### A Small example (32x32)

As a case study, a 32x32 Standard Systolic Array would require 1024 FMAs and would run 1024 multiplications per cycle, consumint 32 inputs and creating 32 outputs per cycle.

An equivalent VSA is 32 units wide, with a VDepth of 32, and a real depth of 1, needing in only 32 FMAs, but only running 32 multiplications per cycle and only outputting 1 output per cycle (1/32nd the calculations).
Also, it only consumes 32 inputs every 32 cycles (basically 1 input per cycle), but it still stores 1024 Weights internally. 

While this looks like a weakness of this design, it's actually the basis of it's advantage.


### Hardware Complexity

Because the Fused Multiply Accumulate (FMA) unit is basically the same for a VSA Unit and a standard Systolic Array Unit, the main difference is the added complexity of the Circular Shift Register.

However, the VSA Unit's complexity should be less than 2x compared to the standard Unit (at least for lower Virtual Depths).

The other complexity is how it attaches Input and Output to the rest of the chip, but because this is based on architecture, I can't make statements on the expected or optimum complexity.

Note: there are a variety of extra features that can be implemented that would increase the complexity significantly. But I would consider them all optional.


## Weaknesses

One of the primary weaknesses of this design is the latency of the system. 
Note: I strongly believe that the benefits of the VSA would actually provide better latency for most real networks.

### Latency 

Because The VSA takes more cycles to get the full outputs, it's possible that networks will take longer from start to finish than they would with a standard Systolic Array.

For a given Virtual Depth of N, it would take N times longer to get the output from an NxM calculation if the Standard Array was a good width for M. 
Because it could take N times longer to get the output needed for the next layer, it could increase the Latency by a factor of N.
However, I believe that in real usage, it will more than make up for this delay with it's strengths.

### Setting Weights

Because of the way (and rate) Data is feed into the VSA, it's possible that setting the weights could take significantly longer (N times longer, or even N squared).

This is primarily an implementation thing, and it's quite possible to be resolved through different methods.


## Strengths

There are multiple strengths that VSAs use to eliminate the weaknesses of Standard Systolic Arrays. 

A VSA can hold more active weights per unit area on a chip, which allows much larger layers to be kept loaded at the same time.

The biggest strength is defined in the architecture, and it is not even specific to VSAs, but becomes impractical for Standard Systolic Arrays.

### Large number of Active Weights 

In our 32x32 example, the Standard Array stored 1024 weights in 1024 FMAs, and the VSA stored 1024 weights in just 32 FMAs.
If we assume that the VSA Unit takes twice the chip area as the Standard, we can still fit 16 times as many VSA Units as we could Standard Units.
This would give us 512 VSA Units, with a total of 16,384 Weights worth of storage (but only half the computational output).

Basically, this allows us to trade having a slower processing speed for a larger number of weights in the Array.
For Layers that exceed the size of the array (32x32 in this case) we save by having 16x fewer weight reloads, or if the total weights in the layer are less than 16,384 we don't have to reload at all for this layer. 

This fact is the basics that enable the other enhancements.

### Early Exits

In the above example, we created a VSA of size 512x1 (VDepth 32) but that would not be very useful unless we had a layer that is 512 inputs long.

So, the simple solution is to break it up into multiple, Arrays, like two 256x1, or four 128x1, or sixteen 32x1 Arrays.
Obviously, every time you split the array size in half, you double the number of outputs possible per cycle.
For a Standard Systolic array, this is pretty scary, such as splitting our 32x32 into four 8x32 arrays means we could have to deal with up to 128 outputs per cycle.
This gets a lot more crazy if you use a larger Array, like 128x128. which if split into 8x128 chunks, gives up to 2,048‬ outputs per cycle.
But what if we split our 512x1 array into 8x1 chunks? 
We'd get a total of 64 outputs per cycle, which is only double what the 32x32 outputs, but would theoretically allow us to process 64 separate (small) layers.
So, even though this is capable of processing a lot more width than the 32x32 (16 times more), it only outputs twice the outputs when split into 8 inputs.
That's because it starts with 1/32 the number of outputs.
Because the output value could just be passed one more over to the next block of VSA Units, it's possible to make the exits optional, or to have a seperate Accumulator.


### Process Multiple Layers at once

This strength is the ability to process multiple layers in one stream of operations, without going off chip (RAM, external processors, etc.)
Note: Google TPU's have this ability (but specifics are not easy to come by). 

This feature is likely to cause a huge amount of complexity and limitations, but it's the only way for any type of Systolic Array to achieve good performance, beyond being just a simple add on math chip that's bandwidth limited.
Complexities: this requires, on Chip Bias Addition, on Chip Results Accumulation, on Chip Activations, on Chip Normalizations, and on Chip Non-NN layers (Max-Pool, Concat, Add, etc)

You might ask, if Google TPUs already have this, how is it a strength?
I cannot speak to Google's implementation, but in our analysis of the standard design, it is likely that switching weights is a costly maneuver (either in chip complexity, or in cycles).
But unless all the weights for both layers can be kept loaded at the same time (128x128 would need 16 reloads just for a single layer), it's impossible to stream from one layer to the next without storing a batch in memory/cache.

That's why TPU V1 has such a large 'cache', but we still filled it completely using the batch size of 128, on 7x7 'pixels'.
So what if we could stream the outputs directly to the next layer, even when we only have the output for a single 'pixel' (or a few pixels)?
If we could consume the outputs of the layer as soon* as it's generated, then we wouldn't need to store the intermediate values except temporarily in cache, that could be cleaned out as soon as it is consumed.

