# Help Wanted

There's a ton of work that needs to be done that is outside my specialty.

Please help! Pretty Please?


# TODO List

Here is the list of all the things that currently need to be done.


## Legal Help

I'm not a lawyer, and have not paid a lawyer on any of the legal language.

If you have legal experience and would like to contribute to updating the terms and special conditions on this project, please contact.


## Documentation Help

This project is primarily Documentation, and as such, needs a lot of help.

### Proof-Reading and Editing

I'm not the best writer, and my spelling and grammar is pretty bad.

Additionally, Documents may not be easily understandable, or need revising.  

Please provide feedback!

You can use Issues, Pull Requests, or Other methods to provide this feedback.

#### TODO

Right now, every document needs to be reviewed and edited. 


### Visualizations and Schematics

In order to be clearly documented, and easily understood, there is a great need for visualizations, graphs, etc.

#### TODO

No Documents have any graphs or visualizations. There are a lot that are needed


## Hardware Implementation Help

I'm not experienced in the processes for designing and implementing an ASIC or other custom electrical circuit. 

I need the help of someone who knows what they are doing to manage this part.

#### TODO

Just about everything, including defining specific goals for the Reference Implementation.


## Testing

I have no experience running simulated tests, testbenches, etc.

I need the help of someone who knows what they are doing to manage this part.

#### TODO

Get everything set up and defined.


## Use Case Creation Help

If you are a DL Practitioner, you can contribute by breaking down Use Cases (Models) and helping define important functionality or important goals.

Because this is from the perspective of the "End User" it can help make sure that the project doesn't over specialize for a specific use case (Like MobilenetV2).

### Identify the needs of Back Propagation

Training a NN requires a lot more work to run both the forward-pass and the backwards-pass/back-prop, than just running the forward-pass.
I have not evaluated the needs of training, so it's not been taken into account yet on the design and goals.

I have a decent understanding of back-prop, but I have not spent enough time to figure out how to implement it in hardware.

#### TODO

Create an in depth evaluation of the needs for back-prop.

Evaluate the challenges of making back-prop work with the existing designs/goals.


### Identify Operations

Identifying all the types of operations executed in the main section of the model.
This is to make sure that the majority of models can be run without off-chip actions. 

Note: Pre-Processing and Post-Processing should be excluded, or documented separately. 

Items that should be considered includes Activations, Normalization, Zero Padding, Merge Layers, Pooling Layers, etc.

In the case of custom code, it should give at least a simple example of the custom code, so it's complexity can be evaluated.

#### TODO

Identify and define multiple use cases.

Define the list of non-matrix operations that should be supported. 

Evaluate creating generic implementations for common functions. 


## Firmware and Compiler

I have no experience in Developing Firmware or Compilers, so, please help.


#### TODO

Pretty much everything, but it's probably waiting on the Hardware Implementation.




# Thanks!

I really appreciate any help that you can provide.

