# 2.2.1 Cycle 2 - Abandoning Phaser.io

## Overview

After working with Phaser for a couple weeks, I have decided to make a last minute switch away from Phaser as I found the library increasingly hard to work with the Matter.js physics library on top of the phaser documentation being very poor. I switched to using p5.js and Matter.js, as p5.js is an easy library to work with and would allow me to focus on directly working with matter rather than through phaser. This change did require me to do more work rendering objects aligned with the physics engine, but once the groundwork has been laid, these engine would be much easier to work with

## Design

### Objectives&#x20;

* [x] Download p5.js and Matter.js
* [x] Make a simple p5.js scene
* [x] Render a square to the screen
* [x] Match the Matter.js physics to the p5.js render
* [x] Make it rain squares

### Usability Features



| Variable Name | Use                                          |
| ------------- | -------------------------------------------- |
| engine        | The Matter.js physics engine instance.       |
| world         | The Matter.js world composite.               |
| ground        | A static rectangle acting as a ground plane. |
| boxes         | An array of the boxes created in the scene   |

### Pseudocode

```
var engine
var world
var boxes = [];
var ground;

function setup(){
    createBounds();
    
    startMatterPhysics(engine, world);
    
    ground = Matter.rectangle(x, y, w, h, static = true)
    //p5 render is in runtime
    
    var box1 = Matter.rectangle(x, y, w, h, static = true)
    var box2 = Matter.rectangle(x, y, w, h, static = true)
    boxes.push(box1, box2)
}

function draw(){   
    new p5Rectangle(ground.x, ground.y, ground.w, ground.h)
    
    for i in boxes
        new p5Rectangle(i.x, i.y, i.w, i.h);
}

function p5Rectangle(x, y, w, h){
    translate(x, y);
    rotate(angle);
    rectMode(CENTER); //needed because p5 defaults to a corner
    rect(0, 0, w, h);
```

This pseudocode shows how I can match the physics up to the graphics between p5.js and Matter.js. This code is a lot compared to the much shorter amount needed in Phaser, but I feel like I now understand how the physics and graphics are put together and how I can manipulate them, which is much easier in this setup.

### Input and runtime creation

For the last objective of making it rain cubes, p5.js has functions for inputs, and by using the mouse's position as the origin for the squares...

```
function mouseDragged(){
  boxes.push(Matter.rectangle(mouse.x, mouse.y, w, h))
```

![Falling squares](<../.gitbook/assets/image (3).png>)

### Challenges

The biggest challenge I had while developing this was with the Matter.js engine and making sure that I set up the correct parts and used the correct capitalisation. I also had problems with the getting p5.js to match the rotation of the squares, but both libraries have good documentation, making finding a solution much easier.

## Testing

### Tests

| Test | Instructions                                 | What I expect                                         | What actually happens                                  | Pass/Fail |
| ---- | -------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------ | --------- |
| 1    | Use p5.js to draw canvas.                    | A canvas to be drawn centrally in the scene.          | A canvas was drawn center top.                         | Pass.     |
| 2    | Draw a square to a Matter.js physics square. | Have a square drawn that is affected by gravity.      | A square is drawn and affected by gravity.             | Pass.     |
| 3    | Create a static ground plane.                | A static plane is created at the bottom of the scene. | A static plane was created at the bottom of the scene. | Pass.     |
| 4    | Give the graphics rotation.                  | The squares rotate when expected to.                  | The square graphics follows the physic rotation.       | Pass.     |
| 5    | Have new squares create with the mouse drag. | New squares are constantly created when dragging.     | New squares are constantly created when dragging.      | Pass.     |

### Evidence

![Ground plane in an empty scene.](<../.gitbook/assets/image (7).png>)

![Single square rendered with physics.](<../.gitbook/assets/image (2).png>)

![Raining squares (mouse is above scene view)](<../.gitbook/assets/image (4).png>)
