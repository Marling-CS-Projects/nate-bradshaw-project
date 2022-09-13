# 2.2.11 Cycle 11 - Eliminating memory leak

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



```javascript
function nextGen() {
    let tempCreatureContainer = []; //new container so I can clear the last gen
    currentGen += 1;

    //find first, second and third best
    findBest();

    for (let i = 0; i < creatureNum; i++) { //half are from num 1
      if (i < creatureNum / 2) { //half use 1st
        tempCreatureContainer[i] = mutateCreature(0, i);
      }
      else { //half use 2nd
        tempCreatureContainer[i] = mutateCreature(1, i);
      }
    }

    console.log(tempCreatureContainer)

    for (let i = 0; i < creatureNum; i++) {
      creatureContainer[i].creatureReset();
      creatureContainer[i].dispose();
    }

    for (let i = 0; i < creatureNum; i++) {
      creatureContainer[i] = tempCreatureContainer[i];
      creatureContainer[i].creatureSetup();
      Composite.add(world, creatureContainer[i].McreatureComposite);
    }

    console.log(world)
    tempCreatureContainer = [];
    bestCreaturesFromLastGen = [];
    timerStarted = false;
  }

  function mutateCreature(ID, index) {
    /*
    //console.log(bestCreaturesFromLastGen, "before")
    creatureContainer[index].copy(bestCreaturesFromLastGen[ID]);
    //console.log(bestCreaturesFromLastGen, "after")
    creatureContainer[index].mutate();
    */
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
    //console.log(tempArray)

    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      if (temp > bestX) {
        bestX = temp;
        firstBestID = i;
      }
    }
    //console.log(firstBestID)
    bestCreaturesFromLastGen.push(creatureContainer[firstBestID].brain)

    tempArray.splice(firstBestID, 1, 0);
    //console.log(tempArray)
    bestX = 0;
    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      //console.log(temp);
      if (temp > bestX) {
        bestX = temp;
        secondBestID = i;
      }
    }
    //console.log(secondBestID)
    bestCreaturesFromLastGen.push(creatureContainer[secondBestID].brain)
    //console.log(bestCreaturesFromLastGen);
  }
}

```

For the TensorFlow memory leak, however, I only needed to add minimal lines of code to use TensorFlow's own function for deleting tensors. The challenge was realising I had to place this after I had copied the best brain from the last generation.

```javascript
//NeualNetwork object
dispose() {
  this.model.dispose();
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

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Initial memory leak</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>Memory leak after putting matter.world into a variable</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>Very slight memory leak caused by tensors</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Memory leak eliminated</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>300 generations with no lag.</p></figcaption></figure>
