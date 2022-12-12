# 2.2.13 Cycle 13 - Alternative Objectives

## Overview

Now I'm happy with the creatures, I now will focus on creating a couple of alternative conditions for them to attempt along with other features to customise the evolution further.

## Design

### Objectives&#x20;

* [x] Add a repeating obstacle mode.
* [x] Add a jump (y axis height) mode.
* [x] Add a dropdown menu to chose between modes.
* [x] Add a generation time slider.
* [x] Add some warn text for when a creature doesn't have enough joints / muscles

### Usability Features

| Variable Name     | Use                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------ |
| obstacleContainer | contains all the obstacle objects.                                                   |
| findBestY()       | Duplicate of the findBestX() function but reworked to find best Y.                   |
| bestY             | The highest y coordinate from all of a creatures joints.                             |
| placeOnGround()   | A function to place a creature on the ground.                                        |
| genTimeSlider     | The slider that the user can adjust to change the length of the generations.         |
| warnText          | The text that tells the user that the creature doesn't have enough joints / muscles. |

### Pseudocode

```javascript
generationTime = slider(5, 20)

mode = dropdown(moveToRight, obstacles, jump)

if (mode == obstacles){
    for (let i = 0; i < 5; i++){
        new Rect(...)
    }
}

if(muscles >= minMuscles && joints >= minJoints){
    if(mode == jump){
      findBestY(generationTime);
    }
    else{
      findBestX(generationTime);
    }
}
else{
    warnText = true
}
```

## Development

### Outcome

Before I can implement any new modes, I will first need to create a p5.js DOM dropdown to allow the user to select different modes. I will also create a slider to allow the user to change the amount of time a generation takes (with text that tells the user the amount of seconds). I also added variables to the changeScene() function to allow the options to be passed onwards into evolution\_Scene.js. I also followed up on a feature from Cycle 7 where I didn't allow the player to progress to the evolution with a very low amount of joints and muscles. This needs to be more visualy clear, so this text with tell the user why the scene isn't changing.

<pre class="language-javascript"><code class="lang-javascript">//creature_Creator.js
let warnText;
let sel;

let genTimeSlider;

let optionsIndex;

this.mySetup = function () {
        //...//
    genTimeSlider = createSlider(5, 20, 10, 1);
    genTimeSlider.center('horizontal');
    genTimeSlider.position(genTimeSlider.position().x + 200, genTimeSlider.position().y);

    warnText = createElement('h5', " "); //setting up position
    warnText.center('vertical');
    warnText.center('horizontal');
    
    sel = createSelect();
    sel.center('horizontal');
    sel.position(sel.position().x - 200, sel.position().y);
    sel.option('Move to right');
    sel.option('Obstacles');
    sel.option('Jump');
    sel.changed(selectionEvent);#
        //...//
}

<strong>this.myDraw = function () {
</strong><strong>        //...//
</strong><strong>    warnText.center('vertical');
</strong>    warnText.center('horizontal');
        
    genTimeSlider.center('horizontal');
    genTimeSlider.position(genTimeSlider.position().x + 200, genTimeSlider.position().y);
    
    sel.center('horizontal');
    sel.position(sel.position().x - 200, sel.position().y);
}

function selectionEvent() {
    if (sel.value() == 'Move to right'){
        optionsIndex = 0;
    }
    else if (sel.value() == 'Obstacles'){
        optionsIndex = 1;
    }
    else if (sel.value() == 'Jump'){
        optionsIndex = 2;
    }

    let item = sel.value();
    background(200);
    text('It is a ' + item + '!', 50, 50);
}

function doneButtonDown() {
    if (creatureComposite.bodies.length &#x3C;= 3 || creatureComposite.constraints.length &#x3C;= 3) {
        warnText.elt.firstChild.data = "Please add more joints / muscles"
        warnText.style('color', '#fc0303');
        warnText.center('vertical')
        warnText.center('horizontal')
        return;
            //...//
    }
}

//put in each button and clicking in canvas to clear the message
warnText.elt.firstChild.data = " "
</code></pre>

Adding obstacles was rather easy, as similarly to the striped background I just needed to have a repeating loop adding in rectangles with collision at an interval.

<pre class="language-javascript"><code class="lang-javascript">//evolution_Scene.js

var obstacleContainer = [];

this.mySetup = function () {
<strong>  if (optionsIndex == 1){
</strong>    for (let i = 0; i &#x3C; 5; i++){
      var obstacle = new MyRect(900 + (500 * i), 1100, 100, 800, { isStatic: true }, world);
      obstacleContainer.push(obstacle);
    }
  }

}
</code></pre>

Implementing the jump mode was more challenging due to needing to define and check for a new objective. This mode also caused a few new problems to pop up and need fixing, but the solution bettered the other modes as well.\
\
Firstly, to implement this I needed each creature to know its own best y position among its joints, similar to tracking their average x. While implementing this and finding unusual errors, I discovered that the y coordinates were inverted. When I compensated for such, I managed to get the function working.

```javascript
//calculate best y
let tempY = 0;
let bestY = 9999;
for (let i = 0; i < McreatureComposite.bodies.length; i++) {
  tempY = McreatureComposite.bodies[i].position.y;

  if (bestY > tempY){
    bestY = McreatureComposite.bodies[i].position.y;
  }
}

this.bestY = bestY;
```

Now to track the best y and select it for evolution as well as displaying correct graphics, I needed to make altered versions of functions and logic for the code to use when jump mode is selected. Altered versions of "camera" movement was also needed to accommodate the y axis movement.

```javascript
this.myDraw = function () {
//...//
  else if(optionsIndex == 2){ //getting bestY of all creatures
    for (let i = 0; i < creatureContainer.length; i++) {
      let tempY = creatureContainer[i].bestY;
      if (tempY < bestY) {
        bestY = tempY;
        firstBestID = creatureContainer[i].McreatureID;
      }
      bestX = creatureContainer[firstBestID].averageX;
    }
  }
  
  const zoom = 0.6;
  const shiftX = -bestX * zoom + width / 2;
  const shiftY = -bestY * zoom + width / 2; //y axis camera movement
  
  if(optionsIndex != 2){
    translate(shiftX, 0)
  }
  else{
    translate(shiftX, shiftY + 100)
  }
//...//
}

function nextGen() {
  if(optionsIndex != 2){
    findBestX();
  }
  else{
    findBestY();
  }
//...//
}

function findBestY() {
    let bestY = 0;
    let tempArray = [];
    for (let i = 0; i < creatureContainer.length; i++) {
      tempArray.push(creatureContainer[i].averageX);
    }

    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      if (temp > bestY) {
        bestY = temp;
        firstBestID = i;
      }
    }
    bestCreaturesFromLastGen.push(creatureContainer[firstBestID].brain)

    tempArray.splice(firstBestID, 1, 0);
    bestY = 0;
    for (let i = 0; i < tempArray.length; i++) {
      let temp = tempArray[i]
      //console.log(temp);
      if (temp > bestY) {
        bestY = temp;
        secondBestID = i;
      }
    }
    bestCreaturesFromLastGen.push(creatureContainer[secondBestID].brain)
  }
}
```

Finally, I found that the creatures would find it hard to evolve because their highest points would be from when they initially spawned, causing absolutely no positive reinforcement. To fix this, I created a function that would lower all the joints of a creature so that the lowest was on the ground.  I call this function only once, just before the creatureComposite is passed into evolution\_Scene.js.

```javascript
function placeOnGround(compositeIn){
  //find lowest Y, lower all to ground
  let tempY = 0;
  let lowestY = -9999;
  let arrayPos = -1;
  for (let i = 0; i < compositeIn.bodies.length; i++) {
    tempY = compositeIn.bodies[i].position.y;
    if (tempY > lowestY){
      lowestY = tempY;
      arrayPos = i;
    }
  }

  let lowerAmount = 800 - lowestY;

  for (let i = 0; i < compositeIn.bodies.length; i++) {
    compositeIn.bodies[i].position.y += lowerAmount;
  }

  return compositeIn; //returned after being dropped to 0
}
```

During this process, I also created a line that would show the player the best height reached for more visual clarity.

```javascript
if(optionsIndex == 2) {
  translate(-shiftX, 0)
  strokeWeight(5);
  stroke(0, 100, 0, 225)
  line(-999, bestY, 5000, bestY)
  strokeWeight(1);
  stroke(0, 0, 0, 225)
}
```

### Challenges

The main challenge of this cycle was implementing the jump mode, as it required unique solutions to its problems.

## Testing

### Tests

| Test | Instructions                                  | What I expect                                                                                                               | What actually happens | Pass/Fail |
| ---- | --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Interact with the slider DOM element.         | The slider to change the amount of time in the text and the amount of time in the next generation.                          | As expected.          | Pass.     |
| 2    | Change the mode to obstacles.                 | Obstacles are present in after pressing done.                                                                               | As expected.          | Pass.     |
| 3    | Change the mode to jump.                      | The goal is changed to getting y height, the camera moves on the y axis and there is a line showing the max height reached. | As expected.          | Pass.     |
| 4    | Create a creature high up and press done.     | The creature to be lowered to the ground.                                                                                   | As expected.          | Pass.     |
| 5    | Create an unsuitable creature and press done. | Warning text to show and scene doesn't change.                                                                              | As expected.          | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (1) (3) (2).png" alt=""><figcaption><p>Warn text.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (5).png" alt=""><figcaption><p>DOM elements working</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (5).png" alt=""><figcaption><p>Jump mode</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (3) (1).png" alt=""><figcaption><p>Obstacle mode</p></figcaption></figure>
