# 2.2.4 Cycle 4 - Collision Filtering

## Overview

Now I have nearly all the building blocks for the simulation, the last one to implement is the collision filtering I had short attempt at in the last cycle.

## Design

### Objectives&#x20;

* [x] Create collision filtering that works to my specification
* [x] Create a system to allow new layers to be defined in loops
* [x] Create mouse control to test collision

### Usability Features

I want the implementation to be easy for me to use in the future to reduce problems&#x20;

As mentioned in the last cycle&#x20;

| Variable Name | Use                                                                            |
| ------------- | ------------------------------------------------------------------------------ |
| catDefault    | The default collision category given to all objects in Matter by default, 0001 |
| cat1          | Collision category 0010                                                        |
| cat2          | Collision category 0100                                                        |

### Pseudocode

The collision categories can be put directly into the options section of an object creation, which I have supported with my custom functions from the last cycle.

```javascript
matter.create(mouseConstraint, canvas);

box1 = new myRect(x, y, w, h, collisionCatagory = cat1, 
                  colidesWith = catDefault and cat1);
                  
box2 = new myRect(x, y, w, h, collisionCatagory = cat2, 
                  colidesWith = catDefault and cat2);
                  
draw(box1, box2);

//these boxes wont collide with eachother.
```

## Development

### Outcome

Through using collisionFilter.catagory to sort groups of objects into layers, I have managed to set up a framework for the collision logic I need for the simulation. I also found and implemented the mouse constraint (mouse interaction) code from the Matter.js docs.

```javascript
const catDefault = 1, //Matter allows the values to be entered as such,
      cat1 = 2,       //but they need to be powers of 2, like binary
      cat2 = 4;
      
var mConstraint;
      
function setup(){
      ...
      var box1 = new MyRect(100, 250, 40, 40, {collisionFilter: {category: cat1} });
      var box2 = new MyRect(150, 250, 30, 30, {collisionFilter: {category: cat2} });
      //by default these boxes have collisionFilter.mask set to 1, or catDefault

      var circle1 = new MyCircle(250, 250, 20, {collisionFilter: {mask: catDefault | cat1} });
      //this circle should only colide with box1 and ignore collision with box2
         
      objects.push(box1);
      objects.push(box2);
      objects.push(circle1);
      
      var canvasMouse = Mouse.create(canvas.elt);
      mConstraint = MouseConstraint.create(engine, { mouse: canvasMouse});
      World.add(engine.world, mConstraint);
      ...
}

function draw(){
      ...
      for (let i = 0; i< boxes.length; i++){
            boxes[i].show() //for each element in list render it
      }
      ...
}
```

### Challenges

Trying to understand how bit fields work and how to abstract way from them was the biggest challenge with implementing collision.\
\
Also, for a future cycle potentially, I need to consider how p5.js renders objects on top of each other, because at the moment it seems like the most recent object rendered is rendered on top of other objects, and I will need to potentially change this if I have a foreground that I always want to be on top of other objects in the rendering order.

## Testing

### Tests

| Test | Instructions                                            | What I expect                                                                                    | What actually happens                        | Pass/Fail |
| ---- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------- | --------- |
| 1    | Drag a square around with the mouse.                    | The square to follow the mouse when dragged.                                                     | The square follows the mouse when dragged.   | Pass.     |
| 2    | Create objects with the collisionFilter in the options. | Objects rendered as normal.                                                                      | Objects rendered as normal.                  | Pass.     |
| 3    | Drag the circle into other objects.                     | Based on the collisionFilter, the circle should only collide with the floor and objects on cat1. | The circle collies with objects as expected. | Pass.     |

### Evidence

![Initial scene, objects not falling through floor](<../.gitbook/assets/image (6) (1).png>)

![Mouse constraint working](<../.gitbook/assets/image (4) (1) (1).png>)

![Circle colliding with box1 as expected](<../.gitbook/assets/image (2) (1) (2).png>)

![Circle not colliding with box2 as expected](<../.gitbook/assets/image (5) (2) (1).png>)

![Box1, box2, circle and constrained boxes collision working as expected](<../.gitbook/assets/image (7) (1).png>)
