# 2.2.11 Cycle 11 - Eliminating Memory Leak

## Overview

In this cycle I will be fixing the memory leak causing intense lag within my program. Doing this I had to research the problem as I was not initially aware of memory leaks in JavaScript and how the happened. Once I fix the leaks, I also want to observe a long running evolution to see how the neural networks behave.

## Design

### Objectives&#x20;

* [x] Remove the memory leaks
* [x] Create and run a 300 generation (50 minutes) evolution

### Usability Features



| Variable Name      |  U̶s̶e̶ How it Caused Memory Leak                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| McreatureComposite | When creating new creatures, I cleared the creatureContainer array but the MyCreature objects and their McreatureComposite's still existed as they referenced each other. This was especially bad because the matter.js physics bodies still had collision but didn't have their p5.js rendering because the function for that was no longer being called, causing invisible collisions to also appear in testing. |
| brain              | Tensors in TensorFlow.js don't get removed from memory by JavaScript garbage collection, so I had to use a TensorFlow.js function to clear it.                                                                                                                                                                                                                                                                     |

### Pseudocode

```javascript
//tensorflow lone tensors (outside a model) cleanup
tf.tidy(() => { //this goes around the tensorflow functions
    /*
    ...
    */
)};

//tensorflow removing a neural network, to be ran after a generation is finished.
this.model.dispose();

//MyCreature
//use matter.js to remove the references to these objects
//so they will be garbage collected
this.creatureReset = function () {
  remove(McreatureComposite.constraints);
  remove(McreatureComposite.bodies);
  remove(world.McreatureComposite);
}
```

## Development

Before I got to the final solution for removing the composite based memory leak that worked, I had and tried a couple other ideas for removing the leak. Firstly I thought I could reload the page to clear the memory, but I knew it would take too long and would be disruptive in other ways. Secondly, I tried to reset the individual positions of nodes and muscles instead of creating new objects. Unfortunately, this caused matter.js to launch all the bodies of the screen due to creating unwanted velocity between the original and new position. I abandoned this due to also realising other values would have to be reset and I couldn't get the already implemented TensorFlow memory leak fix. Finally, I figured out how I could remove the reference to the matter bodies so they could be garbage collected, fixing the memory leak.

### Outcome

Starting of with the biggest cause of memory leak, the bodies objects. I found this as a cause of leak by the increasing amount of matter bodies in memory with each generation.

```javascript
function nextGen() {
    let tempCreatureContainer = []; //new container so I can clear the last gen
    currentGen += 1;

    //find first and second best
    findBest();

    for (let i = 0; i < creatureNum; i++) {
      if (i < creatureNum / 2) { //half use 1st
        //put the new bodies under a different array so I can clear the last gen
        tempCreatureContainer[i] = mutateCreature(0, i);
      }
      else { //half use 2nd
        tempCreatureContainer[i] = mutateCreature(1, i);
      }
    }

    console.log(tempCreatureContainer)

    for (let i = 0; i < creatureNum; i++) {
      creatureContainer[i].creatureReset(); //new function
      creatureContainer[i].dispose(); //deletes lat generations tensors
    }

    for (let i = 0; i < creatureNum; i++) { //creating a new generation
      creatureContainer[i] = tempCreatureContainer[i];
      creatureContainer[i].creatureSetup();
      Composite.add(world, creatureContainer[i].McreatureComposite);
    }

    //ready for next generationcode
    console.log(world)
    tempCreatureContainer = [];
    bestCreaturesFromLastGen = [];
    timerStarted = false;
  }

  function mutateCreature(ID, index) {
    let child = new MyCreature(index, creatureCompositeIn, 2**index, bestCreaturesFromLastGen[ID]);
    child.mutate();
    return child;
  }

  function findBest() {
    let bestX = 0;
    let tempArray = [];
    for (let i = 0; i < creatureContainer.length; i++) {
      tempArray.push(creatureContainer[i].averageX);
    }

    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      if (temp > bestX) {
        bestX = temp;
        firstBestID = i;
      }
    }
    bestCreaturesFromLastGen.push(creatureContainer[firstBestID].brain)

    tempArray.splice(firstBestID, 1, 0);
    bestX = 0;
    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      if (temp > bestX) {
        bestX = temp;
        secondBestID = i;
      }
    }
    bestCreaturesFromLastGen.push(creatureContainer[secondBestID].brain)
  }
}

```

```javascript
//remove all the bodies to make sure 0 references exist to it
this.creatureReset = function () {
  for (let i = 0; i < compositeIn.constraints.length; i++) {
  //matter.js function to remove a reference from a composite
    Composite.remove(McreatureComposite, McreatureComposite.constraints[0]);
  }
  for (let i = 0; i < compositeIn.bodies.length; i++) {
    Composite.remove(McreatureComposite, McreatureComposite.bodies[0]);
  }
  //making sure the renderer also has no references
  McreatureRenderer.splice(0, McreatureRenderer.length);
  //removing the reference from the world composite
  Composite.remove(world, McreatureComposite);
}
```

For the TensorFlow memory leak, however, I only needed to add minimal lines of code to use TensorFlow's own function for deleting tensors. The challenge was realising I had to place this after I had copied the best brain from the last generation. I found this leak by typing tf.memory(); into the console in runtime and observing an increase in the tensor count returned.

```javascript
//NeualNetwork object
dispose() {
  this.model.dispose();
}
```

### Challenges

The challenge of fixing memory leak was finding it in the first place, as I started of clueless to why there was so much memory build up, so I needed to find the cause of the issue blindly before I could even try fixing it.

## Testing

### Tests

| Test | Instructions                                                | What I expect                                                                      | What actually happens                                                           | Pass/Fail |
| ---- | ----------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | --------- |
| 1    | Type tf.memory(); into the console in generation 1 and 2.   | Both to return the same number.                                                    | Both return the same number.                                                    | Pass.     |
| 2    | Run the simulation and create a memory snapshot over 3mins. | The memory usage to stay level.                                                    | The memory usage stayed level.                                                  | Pass.     |
| 3    | Run a 300 generation long simulation.                       | Performance for the 300th generation to be no different from the first generation. | Performance for the 300th generation is no different from the first generation. | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Initial memory leak</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>Memory leak after removing matter.js bodies</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Memory leak after removing used tensors.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (4).png" alt=""><figcaption><p>300 generations with no lag.</p></figcaption></figure>
