
# Random Notes

This is a place for me to keep random notes until there is a good place to incorporate them


## Strides suck

Basically, because (2D) Kernel sizes are almost always odd numbers, they are always going to be prime numbers (for all intents and purposes).

Therefore, if the stride is a size that is not a factor of the kernel (1 or the kernel size), it gets weird/inefficient when laying out in a systolic array.

The only reasonable kernel that is not prime would be 9x9 (which is huge, having 9 times as many weights as a standard 3x3) which could use a stride of 3.

The next size kernel would be 15x15 which could have a stride of 3 or 5, but is so huge it's unheard of. 
Obviously 21, 25, and 27 are out of the question...



## Examples and docs need to consider Conv2D

I've been unintentionally ignoring Conv2D and how the Systolic Array works with Sliding window style inputs



## Other stuff
