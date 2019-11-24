
# Initial Proposed Implementation Specs

This is a Document to define the Specs of the Initial proposal for the reference Implementation. 

Note: Manufacturing specifics and limitations will be ignored whenever possible, especially if it is specific to a single Node or manufacturer.


## High Level Specs

Data Type: bfloat16

VDepth: 32



## Focuses and Trade-offs

Forward Pass Focused; But not specifically inference/prediction focused.

Focused primarily on Convolutional Neural Networks (CNNs) and the common variations there of. 

Throughput Focuses; Expecting batches or streaming data, not one shot data.

Not Focused on setting Weights; Because of the design and goals, it is expected that weights will only be reset a few times per second at most.









