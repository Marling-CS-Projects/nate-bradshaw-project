# 2.2.3 Cycle 3 - Setting up Functions

## Overview

Now I'm using p5.js and Matter together, I want to set up some functions for creating common objects I will need to build the scene and creatures out of.

## Design

### Objectives&#x20;

* [x] Create a square creation function
* [x] Create a circle creation function
* [x] Create a constraint (the Matter.js name for bounds or springs) creation function
* [x] Move these functions into a separate js file
* [ ] Implement collision filtering

### Usability Features

I want the functions to be flexible and have as wide of a scope as possible for their use cases.

| Variable Name     | Use                                                                                                                   |
| ----------------- | --------------------------------------------------------------------------------------------------------------------- |
| this.show         | A nested function that can also be called to tell p5.js to render the object.                                         |
| x, y              | The coordinates I want to create a body at, used in both the square and circle function.                              |
| options           | allows me to pass more complex Matter.js options such as friction or collision filtering into the creation functions. |
| w, h              | The width and height of the square, allowing the creation of rectangles.                                              |
| r                 | The radius of the circle, defining its size.                                                                          |
| body1, body2      | The two bodies that the constraint function attaches to.                                                              |
| length, stiffness | options for constraints that are used in every case that I directly implemented into the function.                    |

### Pseudocode

```
function square (x, y, w, h, options)
    matter.createSquare(x, y, w, h, options)
    
    subFunction show()
        x & y = matchPos()
        rot = matchRotation()
        p5.drawSquare(x, y, w, h, rot)
        
function circle (x, y, r, options)
    matter.createCircle(x, y, r, options)
    
    subFunction show()
        x & y = matchPos()
        p5.drawCircle(x, y)
 
 function constraint (body1, body2, length, stiffness)
    matter.createConstraint(body1, body2, length, stiffness)

    subFunction show()
        p5.drawLine(body1.x, body1.y, body2.x, body2.y)
```

## Development

### Outcome

After I made the square function using a tutorial, I was able to easily recreate it for the circle and constraint as the functions are all fundamentally the same despite some small differences like the circle function not needing to consider rotation when drawing as it looks the same either way (unless I add sprites to the circle in the future) or the constraint function needing a line drawn between two bodies to help visualise its position.

```javascript
function MyRect(x, y, w, h, options){
    this.body = Bodies.rectangle(x, y, w, h, options);
    this.w = w;
    this.h = h;

    World.add(engine.world, [this.body]);

    this.show = function(){
        var pos = this.body.position;
        var angle = this.body.angle;
        
        push();
        translate(pos.x, pos.y);
        rotate(angle);
        rectMode(CENTER); //The default center of a square in p5 is a corner, so
                          //it needs to change to be the same as Matter, in the
                          //center
        rect(0, 0, this.w, this.h);
        pop();
    }
}

function MyCircle(x, y, r, options){
    this.body = Bodies.circle(x, y, r, options);
    this.r = r;

    World.add(engine.world, [this.body]);

    this.show = function(){
        var pos = this.body.position;
        //var angle = this.body.angle;
        
        push();
        translate(pos.x, pos.y);
        rectMode(CENTER);
        circle(0, 0, r*2); //p5 creates a circle using its diameter
        pop();
    }
}

function MyConsraint(body1, body2, length, stiffness){

    var constr = Constraint.create({
        bodyA: body1.body,
        bodyB: body2.body,
        length: length,
        stiffness: stiffness
    })

    World.add(engine.world, constr);

    this.show = function(){
        line(body1.body.position.x, 
             body1.body.position.y, 
             body2.body.position.x, 
             body2.body.position.y);
    }
```

I also put this in a separate file called function\_Bank and put a link to it in the html.

```html
<header class="w3-container w3-red w3-center" style="padding:20px 16px">
    <div style="object-position:center"></div>
        <script src="matter.min.js"></script> <!--Matter.js (local)-->
        <script src="functions_Bank.js"></script> <!--Functions-->
        <script src="https://cdn.jsdelivr.net/npm/p5@1.4.1/lib/p5.js"></script> <!--p5.js(online)-->
        <script src="matter_and_p5_test.js"></script> <!--this is where to put my script-->
    </div> 
</header>
```

The options variable that is passed into the myRect and myCircle functions is very versatile, as if I don't need any custom options, passing nothing or null into it will not cause an error, as Matter.js seemingly just ignores it.

Here's an example of how one of these functions would be used:

```javascript
  function setup() {
    ...
    ground = new MyRect(200, 390, 400, 20, { isStatic: true });
    //creates the Matter.js object with the physics.
    ...
  }
  
  function draw() {
    ...
    ground.show();
    //draws the p5.js object over the top, matching the Matter.js physics object.
    ...
  }
```

### Challenges

For this development cycle, I also wanted to try and set up collision filtering with Matter.js, and I found three options you could pass into an objects creation for this, collisionFilter.group, collisionFilter.mask and collisionFilter.catagory. I tried to create the logic I wanted using collisionFilter.group and collisionFilter.mask due to collisionFilter.catagory using bit fields and seeming to be overall more complicated. However after using and fiddling with the logic, I couldn't get what I wanted, which is having multiple filter categories where objects within that category collide with themselves and the ground and not other categories, which I could't find a way to set up with collisionFilter.group and collisionFilter.mask.

## Testing

### Tests

| Test | Instructions                                               | What I expect                                                  | What actually happens                             | Pass/Fail |
| ---- | ---------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------- | --------- |
| 1    | Draw a square / circle using the function.                 | The function to create and draw the object.                    | An object was created and drawn.                  | Pass.     |
| 2    | Draw a square / circle using the function in another file. | The function to create and draw the object the same as before. | An object was created and drawn.                  | Pass.     |
| 3    | Pass null / nothing into the options in myRect / myCircle. | The function to draw a square with default options.            | The function draws a square with default options. | Pass.     |
| 4    | Pass options into the functions such as isStatic = true.   | The created body will be static and the graphics match this.   | A static body and graphics is created.            | Pass.     |

### Evidence

![Squares, rectangles, a circle and a constrain with a static square all created using my functions.](<../.gitbook/assets/image (8).png>)
