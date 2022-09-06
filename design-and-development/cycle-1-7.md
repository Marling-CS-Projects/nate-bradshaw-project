# 2.2.8 Cycle 8 - Neural Evolution Beginnings

## Overview

In this cycle I will be implementing a neural network using Tensorflow.js and integrating it into my current creature object, with goal at the end of the cycle to have 32 creatures with a unique neural network moving in different ways.

## Design

### Objectives&#x20;

* [x] Import Tensorflow.js
* [x] Create a neural network object to attach to the creatures
* [x] Create logic to have a variable amount of inputs and outputs to the neural network
* [x] Create a predict function for the neural network
* [x] Create a mutation function for the neural network

### Usability Features



| Variable Name | Use                                                         |
| ------------- | ----------------------------------------------------------- |
| brain         | The object containing a neural network added to a creature. |
| inputs        | The inputs fed into the neural network                      |
| outputs       | The outputs received from the neural network.               |

### Pseudocode

```javascript
function MyCreature(..., brain){
    this.brain = NeuralNetwork.createModel();
    
    this.think = function() {   
        let inputs = [];

        for each (constraint in MyCreature){
            inputs[].append(constraint.length)
        }
        
        let output = this.brain.predict(inputs);
        
        moveCreature(output);
    }   
    //...//
}

class NeuralNetwork {
    tf = TesorFlow.js
    constructor(){
        this.model = create new medel()
    }

    predict(inputs){
        outputs = tf.evaluateInputs(inputs);
        return outputs;
    }
    
    createModel(){
        tf.addInputLayers(constraintAmount());
        tf.addHiddenLayers(constraintAmount());
        //2 outputs for each constraint / muscle, so each can be stretched or contracted
        tf.addOutputLayers(constraintAmount() * 2);
    }
```

## Development

### Outcome

I started by researching what I specifically needed from a neural network and and found that Neuroevolution was the best approach, as it learns through a generation structure with random mutations, so it was well aligned with my goals. I also used tensorflow.js as it is a reliable and easy to use neural network library. This meant I didn't have to go fully in depth with the mathematics of neural networks and streamline the process.\
\
I also wanted the neural network to have a variable size based on the players creature, so I made sure to leave to amount of nodes to a variable. I was also anticipating the need to evolve and alter the neural networks for each generation so I implemented code and functions to handle that down the line.

```javascript
class NeuralNetwork {
    constructor(a, b, c, d) {
      if (a instanceof tf.Sequential) { //for future generations
        this.model = a;
        this.input_nodes = b;
        this.hidden_nodes = c;
        this.output_nodes = d;
      } else { //initial creation of the neural network
        this.input_nodes = a;
        this.hidden_nodes = b;
        this.output_nodes = c;
        this.model = this.createModel();
      }
    }
```



### Challenges



## Testing

### Tests

| Test | Instructions | What I expect | What actually happens | Pass/Fail |
| ---- | ------------ | ------------- | --------------------- | --------- |
| 1    |              |               |                       |           |
| 2    |              |               |                       |           |
| 3    |              |               |                       |           |

### Evidence

\*screenshots\*
