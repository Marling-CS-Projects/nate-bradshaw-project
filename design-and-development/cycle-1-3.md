# 2.2.4 Cycle 4 - Collision Filtering

## Overview

Now I have nearly all the building blocks for the simulation, the last one to implement is the collision filtering I had short attempt at in the last cycle.

## Design

### Objectives&#x20;

* [x] Create collision filtering that works to my specification
* [x] Create a system to allow new layers to be defined in loops

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


box1 = new myRect(x, y, w, h, collisionCatagory = cat1, 
                  colidesWith = catDefault and cat1);
                  
box2 = new myRect(x, y, w, h, collisionCatagory = cat2, 
                  colidesWith = catDefault and cat2);
                  
draw(box1, box2);

//these boxes wont collide with eachother.
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
