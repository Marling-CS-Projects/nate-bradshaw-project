# 2.2.9 Cycle 9 - Camera and P5js improvements

## Overview

To move forward with the neural evolution I will need to improve the camera in the evolution scene to allow the user to see the creatures and stop them from going offscreen. Along with this, I will implement a few improvements to my p5.js code give more visual clarification. I will also limit the expansion and contractions of the creatures muscles.

## Design

### Objectives&#x20;

* [x] Zoom out the camera
* [x] Make camera panning
* [x] Add visual improvements

### Usability Features

I have decided to have the camera follow the creature in the lead and to keep the zoom at a constant distance.

| Variable Name | Use                                               |
| ------------- | ------------------------------------------------- |
| zoom          | Constant zoom value                               |
| shiftX        | How far on the x axis the view has to be shifted. |
| bestX         | The average x coordinates of                      |
| firstBestID   | The id of the creature with the best average x    |

### Pseudocode

Camera pseudocode:

```javascript
//evolution_scene.js
let bestX = 0;
for(let i = 0; i < creatureContainer.length; i++){
    findBestX();
  }
}

const zoom = zoom;
translateCamera(zoom, bestX);
```

For the visual improvements, I found whilst developing and creating a creature to test, adding the muscles was sometimes confusing as I couldn't visually see if I had already chosen a start node or not, and I could also make a 0 length constraint between the same body. To fix this I will add a few checks:

```javascript
//temp is the first clicked node
if (temp == clickedOn) {
    temp = null;
}
//this changes the logic to clear the constraint if you click on the same node again

//draws a line from the node clicked to the mouse when only 1 node has been selected
if (temp != null) {
    drawLine(temp, mouse.position);
}
```

And for limiting the constraints, for now I will just limit the size a constraint can shrink or grow to and then implement this logic better into the neural network later on.

```javascript
//finish this psuedocode
if (McreatureComposite.constraints[maxVal / 2].length <= compositeIn.constraints[maxVal / 2].length + 200) {
  McreatureComposite.constraints[maxVal / 2].length += 5;
}

if (McreatureComposite.constraints[(maxVal - 1) / 2].length > 30 &&
  McreatureComposite.constraints[(maxVal - 1) / 2].length >= compositeIn.constraints[(maxVal - 1) / 2].length - 200) {
  McreatureComposite.constraints[(maxVal - 1) / 2].length -= 5;
}
```

## Development

### Outcome

p5.js doesn't have a camera unless you use WEBGL, which can't easily be used with my project due to the added work of making 2D work in WEBGL. I instead found a better solution, where you translate all objects in the scene opposite to where you would imagine a camera moving. To do this, you can translate and scale the entire canvas. This also effects the matter.js physics bodies, essentially acting like a camera.\
\
To find the best x position, I quickly wrote a loop that runs through all of the average X positions from each creature, that they individually calculated from the x positions of all their circles. I then use this to tell p5.js how much to move the canvas view so the camera follows the best creature at all times.

```javascript
//evolution_scene.js
let bestX = 0;
for(let i = 0; i < creatureContainer.length; i++){
  let temp = creatureContainer[i].averageX;
  if(temp > bestX && firstBestID != creatureContainer[i].McreatureID){
    bestX = temp;
    firstBestID = creatureContainer[i].McreatureID;
  }
}

const zoom = 0.6;
const shiftX = -bestX * zoom + width / 2;
push();
translate(shiftX, 0);
scale(zoom);
background(51);
ground.show(); //rendering the ground box
for (let i = 0; i< creatureContainer.length; i++){
  creatureContainer[i].show(); //for each element in list render it
  creatureContainer[i].think(); //neural network movement
}
pop();

//function_bank.js
//MyCreature
let tempX = 0;
for (let i = 0; i < McreatureComposite.bodies.length; i++) {
  tempX += McreatureComposite.bodies[i].position.x;
}
averageX = tempX / McreatureComposite.bodies.length;
this.averageX = averageX;
```

The visual changes to creature\_creator.js however were a lot more simple to implement, with taking the constraint code from the function I created in Cycle 3 and removing the physics properties.

```javascript
//temp is the first clicked node
else if (temp == mConstraint.body) {
    temp = null;
}
//this changes the logic to clear the constraint if you click on the same node again

//draws a line from the node clicked to the mouse when only 1 node has been selected
if (temp != null) {
    strokeWeight(10);
    line(temp.position.x,
        temp.position.y,
        mouseX,
        mouseY);
    strokeWeight(1);
}
```

Finally, to limit the movement of the creatures constraints I first need to consider how far I need to allow them to extend and contract

```javascript
if (maxVal % 2 == 0) {//even
  if (McreatureComposite.constraints[maxVal / 2].length <= compositeIn.constraints[maxVal / 2].length + 200) {
    McreatureComposite.constraints[maxVal / 2].length += 5;
  }
}
else {//odd
  if (McreatureComposite.constraints[(maxVal - 1) / 2].length > 30 &&
    McreatureComposite.constraints[(maxVal - 1) / 2].length >= compositeIn.constraints[(maxVal - 1) / 2].length - 200) {
    McreatureComposite.constraints[(maxVal - 1) / 2].length -= 5;
  }
}
```

### Challenges

The biggest challenge was finding relevant information for a non WEBGL camera for p5.js, but other than that the implementation was a welcome break in difficulty.

## Testing

### Tests

| Test | Instructions                                                                           | What I expect                                     | What actually happens                          | Pass/Fail |
| ---- | -------------------------------------------------------------------------------------- | ------------------------------------------------- | ---------------------------------------------- | --------- |
| 1    | In creature\_creator.js, create a node and create the start of a muscle from the node. | A line to be rendered from the node to the mouse. | A line is rendered from the node to the mouse. | Pass.     |
| 2    |                                                                                        |                                                   |                                                |           |
| 3    |                                                                                        |                                                   |                                                |           |

### Evidence

\*screenshots\*
