# 2.2.10 Cycle 10 - Neural Evolution Generations

## Overview

With now both a neural network and canvas panning set up, I can now finish the neural evolution by implementing generations to allow the neural network to improve with time.

## Design

### Objectives&#x20;

* [x] Determine the best and second best preforming creature from the last generation
* [x] Use the neural network of the best and second best preforming creatures to create a new generation
* [x] Mutate the brains randomly for the new generation
* [x] Create a graphic to tell the user the current generation and current best creature

### Usability Features



| Variable Name  | Use                                                                |
| -------------- | ------------------------------------------------------------------ |
| currentGen     | The current generation number.                                     |
| firstBestID    | The ID of the best creature from the last generation.              |
| secondBestID   | The ID of the second best creature from the last generation.       |
| modelCopy      | The copy of a neural network fed into MyCreature.                  |
| mutatedWeights | The mutated weights value after being calculated by Tensorflow.js. |

### Pseudocode

```javascript
function nextGen(){
  currentGen += 1;

  //find first and second
  best = findBest();
  secondBest = findSecondBest();

  //empty creatureContainer
  creatureContainer = [];
  
  createNewGeneration(creatureContainer);
  mutate(creatureContainer);
}
```

## Development

### Outcome

To implement this I had to add 2 more functions to the neural network object to handle copying a brain and mutating it. I need to copy the best brain from last generation for each new creature so they all have a reference to their own individual brain. I'll do this by allowing a brain to be passed into MyCreature to be copied. I'll also put a mutate() function inside MyCreature to allow it to be called easily in the evolution\_scene.js script.

<pre class="language-javascript"><code class="lang-javascript">function MyCreature(brain) {
    this.brain = brain;
    if (brain) { //if brain != null
        this.brain = brain.copy();

    this.mutate = function() {
        this.brain.mutate(0.1);
    }
<strong>}
</strong><strong>
</strong><strong>//NeuralNetwork()
</strong><strong>copy() {
</strong>  const modelCopy = this.createModel();
  const weights = this.model.getWeights();
  const weightCopies = [];
  for (let i = 0; i &#x3C; weights.length; i++) {
    weightCopies[i] = weights[i].clone();
  }
  modelCopy.setWeights(weightCopies);
  return new NeuralNetwork(
    modelCopy,
    this.input_nodes,
    this.hidden_nodes,
    this.output_nodes
  );
}
  
mutate(rate) {
  const weights = this.model.getWeights();
  const mutatedWeights = [];
  for (let i = 0; i &#x3C; weights.length; i++) {
    let tensor = weights[i];
    let shape = weights[i].shape;
    let values = tensor.dataSync().slice();
    for (let j = 0; j &#x3C; values.length; j++) {
      if (random(1) &#x3C; rate) {
        let w = values[j];
        values[j] = w + randomGaussian();
      }
    }
    let newTensor = tf.tensor(values, shape);
    mutatedWeights[i] = newTensor;
  }
  this.model.setWeights(mutatedWeights);
}</code></pre>

To put the generations on a timer, I will use setTimeout(), a JavaScript function that calls a function after a time in milliseconds.

```javascript
if(!timerStarted){
  setTimeout(nextGen, 1000); //10 secs
  timerStarted = true;
}
```

After that, for the logic in evolution\_scene.js, when nextGen is called, It finds the best and second best creatures, puts their ID into an array and from that ID, the brain of that creature can be copied and mutated for a new creature in the next generation.

```javascript
function nextGen(){
  currentGen += 1;

  //find first, second and third best
  findBest()

  creatureContainer.splice(firstBestID, 1);
  if (firstBestID < secondBestID){
    creatureContainer.splice(secondBestID - 1, 1);
  }
  else{
    creatureContainer.splice(secondBestID, 1);
  }

  for (let i = 0; i < creatureNum - 2; i++){
    creatureContainer[i].dispose();
  }

  creatureContainer = [];

  for (let i = 0; i < creatureNum; i++) { //half are from num 1
    if(i < creatureNum / 2){ //half use 1st
      creatureContainer[i] = mutateCreature(0, i);
    }
    else{ //half use 2nd
      creatureContainer[i] = mutateCreature(1, i);
    }
  }
  for (let i = 0; i < creatureNum; i++){
    creatureContainer[i].creatureSetup();
  }
  bestCreaturesFromLast = [];
  timerStarted = false;
}

function mutateCreature(ID, index) {
  let child = new MyCreature(index, creatureCompositeIn, 2**index, bestCreaturesFromLast[ID].brain);
  child.mutate();
  return child;
}

function findBest() {
  let bestX = 0;
  let tempArray = [];
  for(let i = 0; i < creatureContainer.length; i++){
    tempArray.push(creatureContainer[i].averageX);
  }
  for(let i = 0; i < tempArray.length; i++){
    let temp = tempArray[i]
    if(temp > bestX){
      bestX = temp;
      firstBestID = i;
    }
  }
  bestCreaturesFromLast.push(creatureContainer[firstBestID])

  tempArray.splice(firstBestID, 1, 0);
  bestX = 0;
  for(let i = 0; i < tempArray.length; i++){
    let temp = tempArray[i]
    if(temp > bestX){
      bestX = temp;
      secondBestID = i;
    }
  }
  bestCreaturesFromLast.push(creatureContainer[secondBestID])
}
```

Finally, I added some simple text to the canvas using p5.js, and if I place it in the canvas after its been transformed, the text stays still on the canvas even when the rest of it pans. I also added another line of text to tell the user which creature was doing the best and its position.

```javascript
stroke(0);
fill(150);
textSize(32);
text('Generation: ' + currentGen, 0, 42);
text(("Current Best Creature: " + (firstBestID + 1) + " at " + parseInt((bestX - startingPos))), 0, 72)
fill(255);
```

### Challenges

Learning the concepts of neural networks to be able to understand the process going on within Tensorflow.js has been the greatest challenge of this cycle. Luckily, back propagation isn't required for neuro evolution, as there is no training data needed, as the neural network evolves on its own

## Testing

### Tests

| Test | Instructions                                                                                               | What I expect                                                                                                          | What actually happens                                                                                               | Pass/Fail |
| ---- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------- |
| 1    | In evolution\_scene.js, wait for a new generation.                                                         | The generation counter to go up 1 and a new set of creatures to be created in the same place as the original creatures | The generation counter goes up 1 and a new set of creatures are created in the same place as the original creatures | Pass.     |
| 2    | Leave evolution\_scene.js running for 50 generations to see if there is improvements in the neural network | The creatures become better at moving to the right.                                                                    | The scene starts lagging around generation 10 and further lags with each generation until its too slow to run.      | Fail.     |

Looking into this lag issue, I found that more and more memory is being used each generation, which is a big problem as it makes the program completely unusable in a web application. This memory leak will have a dedicated cycle to get rid of it. Unfortunately, this means evolution can't be properly observed and tested until this is fixed.

### Evidence

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>Text working.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption><p>New generation created.</p></figcaption></figure>
