
# Impossible Goal

Run (standard) Mobilenets V2 from begining to end on one device without memory accesses or buffering.


## Why try if it's Impossible?

I'm a firm beleiver in trying for impossible goals if the result of failing or falling short is still better than the result of aiming for a reasonable goal.


## Mobilenets V2 Goal

Basically, for this goal, the weights should never be reloaded, and most, or all the layers should not be bottlenecked by waiting for the previouslayer outputs to fill a buffer .

Note: Because Mobilenet is configurable with different settings, I'm using the structure of a simple version that has been widly distributed.


### Caveats

I will ignore the last layers that are used to create the categorical output, besides being easily changed for other problems, this layer is very easy for a CPU or GPU to run alone.

Any Memory that is used as an interconnect between multiple devices will be ignored for sake of the goal, if it's reasonable.

Note: I may have also made a mistake, but it seems that the stride of 2 can be pushed up to the DW layer.


### Some quick math

Note: This math is assuming near perfect reuse/sharing of already calculated values.

The last layer I'm going to consider has size 7x7, for simplicity(?) lets only consider calculating one of these 49 points at a time. 
The raw input has size 224x224, resulting in each of the 7x7 ouputs being responsible for 32x32 unique inputs.
That's 1024 pixels input per output, and likely gonig to cause the computational imposibility. 

Note: I may have made mistakes in my math here, sorry, it's late and there's alot of layers.

The first 3 color channels are converted to 32 outputs using a 3x3 kernel with stride 2, resulting in a 112x112x32 block.
The inputs for each of the 32 filters would be 9x3, giving a total of 112x112 blocks of 27x32 multiplication
Followed by a 3x3 depthwise and a squeze to 16

Note:(112x112 / 7 = 16x16)
Running Total: 
Conv2d s2: 16x16 x 9x3x32 = 221,184
DW: 16x16 x 9x32 = 73,728
1x1: 16x16 x 32x16 = 131,072
exp: 16x16 x 16x96 = 393,216‬
DW s2: 8x8 x 9x96 = 55,296
1x1: 8x8 x 96x24 = 147,456‬
exp: 8x8 x 24x144 = 221,184
DW: 8x8 x 9x144 = 82,944
1x1: 8x8 x 144x24 = 221,184
exp: 8x8 x 24x144 = 221,184
DW s2: 4x4 x 9x144 = 20,736
1x1: 4x4 x 144x32 = 73,728
exp: 4x4 x 32x192 = 98,304‬
DW: 4x4 x 9x192 = 27,648‬
1x1: 4x4 x 192x32 = 98,304
exp: 4x4 x 32x192 = 98,304‬
DW: 4x4 x 9x192 = 27,648‬
1x1: 4x4 x 192x32 = 98,304
exp: 4x4 x 32x192 = 98,304‬
DW s2: 2x2 x 9x192 = 6,912
1x1: 2x2 x 192x64 = 49,152‬
exp: 2x2 x 64x384 = 98,304‬
DW: 2x2 x 9x384 = 13,824
1x1: 2x2 x 384x64 = 98,304
exp: 2x2 x 64x384 = 98,304‬
DW: 2x2 x 9x384 = 13,824
1x1: 2x2 x 384x64 = 98,304
exp: 2x2 x 64x384 = 98,304‬
DW: 2x2 x 9x384 = 13,824
1x1: 2x2 x 384x64 = 98,304
exp: 2x2 x 64x384 = 98,304‬
DW: 2x2 x 9x384 = 13,824
1x1: 2x2 x 384x96 = 147,456
exp: 2x2 x 96x576 = 221,184‬
DW: 2x2 x 9x576 = 20,736
1x1: 2x2 x 576x96 = 221,184‬
exp: 2x2 x 96x576 = 221,184‬
DW: 2x2 x 9x576 = 20,736
1x1: 2x2 x 576x96 = 221,184‬
exp: 2x2 x 96x576 = 221,184‬
DW: 1x1 x 9x576 = 5,184‬
1x1: 1x1 x 576x160 = 92,160
exp: 1x1 x 160x960 = ‬153,600‬
DW: 1x1 x 9x960 = 8,640‬
1x1: 1x1 x 960x160 = 153,600‬
exp: 1x1 x 160x960 = ‬153,600‬
DW: 1x1 x 9x960 = 8,640‬
1x1: 1x1 x 960x160 = 153,600‬
exp: 1x1 x 160x960 = ‬153,600‬
DW: 1x1 x 9x960 = 8,640‬
1x1: 1x1 x 960x320 = 307,200‬


Total of: 5,702,528 Weights
with 16 bit weights it should fit inside 12MB of storage.





