
# Why AGPL?

Put Simply, it's to force an open-source Reference Implementation to be published.

Note: I don't care if commercial implementations of the Reference Implementation are closed source proprietary Bull$%&#, so long as at least one reference implementation is properly open.


## Why a Reference Implementation

Without a common (non-proprietary) Implementation that uses an open and available instruction set; 
then every company will release incompatible, tightly licensed, and ridgid implementations.

This will be disastrous for Deep Learning Practitioners (like myself) because it'll be impossible to create one model that runs well on all the different implementations.

As a reference, look at the lack of information on Google's TPUs, which makes optimizing for it hard and will make cross compilation a challenge. 
As a specific example, I tried to find the specs on the Google Edge Coral TPU, and I'm not even sure if it uses a Systolic Array (but I'm pretty sure it does).
Worse, I have no idea what the size of the Systolic Array it has (if there is one). 
If you look at my breakdown of performance, you'll see that using a model with input or output channels just larger than the Arrays width (like 130 on a 128 array) causes huge speed loss, but losing only 2 channels at training time would have almost no noticeable effect.


###  Cross Compatibility

As Deep Learning continues to grow (and the compute required grows) we will see the deployment of NN Accelerators like the edge TPU become much more common.
But, what if every company that makes a NN Accelerator builds their own implementation, instructions, etc. such that they are completely proprietary.

It'll get to the point that to deploy a Model that uses acceleration, it'll have to be compiled for many different hardware configurations.

You can see a pattern in many other spaces, where incompatibility between devices has made life hard for those using it.
Even today, there are tons of Apps only available on IPhone or Android, simply because cross compatibility is such a pain.
But note that 99% of these cases, the Apps don't use any feature that is exclusive to the device of one brand or the other, it's just that the native code cant be cross compiled.


## x86

Would personal computing be the same if x86 hadn't become the defacto (and available from multiple vendors)? 

Would personal computing be different or better if the x86 specifications and instruction set had been more open?

I can't answer these theoretical questions, but I can be sure that NN Accelerators need to have open standards and instruction sets.
If they don't, it will likely set back their adoption by years; after-all, would you pay an extra $50 for a chip that will only speed up 3 Apps on your phone?


## That doesn't explain it

Nope, but it explains why there needs to be a Reference Implementation, and this license seems like the best way to force it.


# Public Domain

Once there is a good (and open) Reference Implementation, then there is no need to maintain this aggressive license, since it will stifle development.
Additionally, All the work done here will be relicensed as CC0/Public Domain, to allow companies to freely develop and expand the implementation.


## Commercial Development before the completion of the Reference Implementation

While the Lawyers may argue over this, I don't believe that developing a product based on this before the completion of the Reference Implementation  is a violation.
But, running that implementation in a production environment, shipping it in products, or other similar commercial uses of it are a violation.
Any violations that happened before the license change will still be pursued after the license change. 


### Commercial Development of the Reference Implementation

Please note: this project does not have to be the creator of the Reference Implementation.
Please note: the Reference Implementation does not have to be commercially viable, but must meet a set of minimum requirements.
If any company (or open-source project) releases a viable Reference Implementation under a sufficiently open license* (must allow derivatives, must not limit commercial use, no fees, no exclusions) *subject to change,
then this license will be converted, upon completing all the required due diligence, etc.




