# 2.2.8 Cycle 8 - Neural Evolution Beginnings

## Overview

In this cycle I will be implementing a neural network using Tensorflow.js and integrating it into my current creature object, with goal at the end of the cycle to have 32 creatures with a unique neural network moving in different ways.

## Design

### Objectives&#x20;

* [x] Import Tensorflow.js
* [x] Create a neural network object to attach to the creatures
* [x] Create logic to have a variable amount of inputs and outputs to the neural network
* [x] Create a predict function for the neural network

### Usability Features



| Variable Name | Use                                                         |
| ------------- | ----------------------------------------------------------- |
| brain         | The object containing a neural network added to a creature. |
| inputs        | The inputs fed into the neural network                      |
| outputs       | The outputs received from the neural network.               |

### Pseudocode

```javascript
function MyCreature(brain){
    this.brain = new NeuralNetwork();
    
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
    constructor(){
        addInputLayers(constraintAmount());
        addHiddenLayers(constraintAmount());
        //2 outputs for each constraint / muscle, 
        //so each can be stretched or contracted
        addOutputLayers(constraintAmount() * 2);
    }

    predict(inputs){
        outputs = evaluateInputs(inputs);
        return outputs;
    }
```

## Development

### Outcome

I started by researching what I specifically needed from a neural network and and found that Neuroevolution was the best approach, as it learns through a generation structure with random mutations, so it was well aligned with my goals. I also used tensorflow.js as it is a reliable and easy to use neural network library. This meant I didn't have to go fully in depth with the mathematics of neural networks and streamline the process.\
\
I also wanted the neural network to have a variable size based on the players creature, so I made sure to leave to amount of nodes to a variable.

```javascript
class NeuralNetwork {
    constructor(a, b, c) {
        this.input_nodes = a;
        this.hidden_nodes = b;
        this.output_nodes = c;
        this.model = this.createModel();
    }
    
    createModel() { //this is the neural network
      const model = tf.sequential();
      const hidden = tf.layers.dense({
        units: this.hidden_nodes,
        inputShape: [this.input_nodes],
        activation: 'sigmoid' //puts the values between 0 and 1
      });
      model.add(hidden);
      const output = tf.layers.dense({
        units: this.output_nodes,
        activation: 'softmax' //puts the values between 0 and 1, will all adding to 1
      });
      model.add(output);
      return model;
    }
```

Finally, for the initial neural network to run, I needed to run some inputs through it and then receive an output.

```javascript
predict(inputs) {
    const xs = tf.tensor2d([inputs]);
    const ys = this.model.predict(xs);
    const outputs = ys.dataSync();
    return outputs;
}
```

With these created, I just needed to add some functions to the creature object to integrate the neural network.

```javascript
this.think = function() { //to be ran in the draw function
    let inputs = [];
    for(let i = 0; i < McreatureComposite.constraints.length; i++){
        inputs[i] = McreatureComposite.constraints[i].length
    }
    let output = this.brain.predict(inputs);

      // 0 - increase constraint [0] 
      //1 - decrease constraint [0] 
      //2 - increase constraint [1] 
      //3 - decrease constraint [1]

    const maxVal = output.indexOf(Math.max(...output)); //find largest output
    
    if(maxVal % 2 == 0) {//even, grow muscle
      McreatureComposite.constraints[maxVal / 2].length += 5;
    }
    else{//odd, shrink muscle
      McreatureComposite.constraints[(maxVal - 1) / 2].length -= 5;
    }
}
```

### Challenges

Getting the custom inputs and outputs size was confusing and took a little while to implement along with sorting out errors caused by another bit of code interacting with the neural network.

## Testing

### Tests

| Test | Instructions                           | What I expect                                                  | What actually happens                                       | Pass/Fail |
| ---- | -------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------- | --------- |
| 1    | Create a creature with 3 constraints.  | The neural network to  make the creatures move in unique ways. | The neural network makes the creatures move in unique ways. | Pass      |
| 2    | Create a creature with 10 constraints. | The neural network to  make the creatures move in unique ways. | The neural network makes the creatures move in unique ways. | Pass      |

The results do show, despite being a successful demonstration of the neural network working, that restraints on the extent of extension and retraction should be put in place.

### Evidence

<figure><img src="../.gitbook/assets/image (2) (4) (2).png" alt=""><figcaption><p>Constraints being lengthened / shortened by the neural network, 3 for each creature</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (3).png" alt=""><figcaption><p>Constraints being lengthened / shortened by the neural network, 10 for each creature</p></figcaption></figure>
