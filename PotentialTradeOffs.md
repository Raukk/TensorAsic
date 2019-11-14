
# Potential Trade Offs

There are always a large variety of trade offs when designing and building something, and oftentimes the right choice is dependent on the desired result or expected use.



## Int8 or BFloat16 (or other)

The two are used in Google’s TPUs, they use Int8 when Inference only is the goal and BFloat16 when training is also a goal.


### Int8 Advantages

Int8 will give you the most bang for your buck transistor wise (in an FMA), that's not likely to ever be disputed.

### Int8 Disadvantages

Int8 cannot be used to train or retrain a model.

Int8 requires the model be carefully trained and quantized before being deployed. 

Int8 and/or quantization cause a decrease in accuracy of the results.


### BFLoat16 Advantages

Comparable to FP32 in accuracy, range, etc. It's easy to convert between FP32 BFloat16.

Smallest number of transistors for an FMA of any Float Implementation. Except maybe Float8, but that's not really comparable.

### BFLoat16 Disadvantages

Not really much that I'm aware of. 



## Input Fall Through VS Input Cloning

This may not make much sense, but consider a 32x32 Systolic array.

The same functionality could be achieved with 1024 FMA's in a row, where inputs are repeated ever 32 units.

Sadly, I don't have a deep enough understanding to truly evaluate their pros and cons.

### Input Fall Through Advantages

Simplified design, more compact design, more math with fewer inputs. 

### Input Fall Through Disadvantages

Cannot distinctly utilize excess compute depth.

### Input Cloning Advantages

More flexibility

More Compute throughput in the Systolic Array

No fall through delay

### Input Cloning Disadvantages

More complexity

More routing


