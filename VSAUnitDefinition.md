
# The "Virtual Systolic Array Unit" (VSA Unit)

The "Virtual Systolic Array Unit" is similar to a Fused Multiply Accumulate (FMA) that is used in a Standard Systolic Array.

While a Systolic Array Unit stores only one weight value which is multiplied by a new input each cycle. 

A Virtual Systolic Array Unit stores N weights (instead of one). 

It stores them in a circular shift register (or similar) that loops the weight values indefinitly.

## Diagrams

![alt text](https://github.com/Raukk/TensorAsic/blob/master/VSA%20Unit%20Diagram.svg "SVG of the VSA Unit")


## Addtional Info.

[Other details availible here](https://github.com/Raukk/TensorAsic/blob/master/SolvingWeaknesses.md)



