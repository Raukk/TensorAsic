# Reference Implementation

The point of this Document is to define some high level targets and processes for creating the Reference Implementation.


## Target Specs

These are a guess at what specs would be good for the device. 
Assumptions and guesses will be validated and corrected over time.

### Hardware 

#### FMA

BFloat16 Mult, Float32 Add

#### VDepth

Expected: 32 Virtual Units

Range: 16 to 64 Virtual Units

#### Early Exit 

Expected: 100 Units per Early Exit 

Range: 10 to 300 Units per Exit


#### Total Units 

Expected: 16,384 Units per Chip

Range: 8,192‬ to 65,536‬ Units per Chip 


#### Clock Speed

Expected: Unknown (but use 1Ghz to simplify calculations)

Range: 700Mhz to 1.3 Ghz (based on known TPU values)












