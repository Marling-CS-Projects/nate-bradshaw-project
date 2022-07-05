# 2.2.1 Cycle 2 - Abandoning Phaser.io

## Overview

After working with Phaser for a couple weeks, I have decided to make a last minute switch away from Phaser as I found the library increasingly hard to work with the Matter.js physics library. I switched to using p5.js and Matter.js, as p5.js is an easy library to work with and would allow me to focus on directly working with matter rather than through phaser. This change did require me to do more work rendering objects aligned with the physics engine, but once the groundwork has been laid, these engine would be much easier to work with

## Design

### Objectives&#x20;

* [x] Download p5.js and Matter.js
* [x] Make a simple p5.js scene
* [x] Render a square to the screen
* [x] Match the Matter.js physics to the p5.js render
* [x] Make it rain cubes

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



```

## Development

### Outcome



```
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
