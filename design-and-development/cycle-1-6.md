# 2.2.7 Cycle 7 - Simulation Scene Beginnings

## Overview

For this cycle, I will be taking the creature created by the player and putting it into a new scene as an object that contains the composite with the physics objects and a draw function to render on top of the physics objects. This object will contain the neural evolution code in future cycles.

## Design

### Objectives&#x20;

* [x] Have the player created creature in a new scene.
* [x] Have multiple of the player created creatures in a new scene.
* [x] Have all of the different creatures not collide with each other.

### Usability Features

The implementation of this is yet another addition to the function\_Bank script, with the creature physics and graphics being contained in a single object.

| Variable Name          | Use                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------ |
| creatureCompositeIn    | The composite from the creature\_Creator scene, to be used in the MyCreature() function.   |
| creatureContainer      | An array containing all of the creatures created in the scene.                             |
| McreatureComposite     | The composite holing all the "joints" (circles) and "muscles" (constraints) of a creature. |
| McreatureRenderer      | The list of objects to be rendered over the physics objects of a creature.                 |
| McreatureID            | The ID of a creature.                                                                      |
| McreatureColisionLayer | The collision category of a creature.                                                      |

### Pseudocode

```javascript
function MyCreature(McreatureID, creatureCompositeIn, McreatureColisionLayer){ 
    this.creatureSetup = function(){
        McreatureComposite = new Composite.create();
        for(i in creatureCompositeIn[i]){ //recreating the creature created by the player
            McreatureRenderer.push(new object)
            McreatureComposite.push(new object)
        }
    }

    this.show = function(){     
        for (i in McreatureRenderer[i]){
            McreatureRenderer[i].show() //renders each element
        }
    }
}
```

## Development

### Outcome

I started out by creating a bare bones scene and a "Done" button to the creatureCreator.js script which then uses the scene switching script from cycle 5 to switch to evolution\_Scene.js. I also passed the creature composite the player made through to the evolution scene to be used in some code shortly. I also created some checks to make sure the player couldn't move forward until they had placed down the minimum amount of objects to make a viable creature.

{% tabs %}
{% tab title="evolution_Scene.js" %}
```javascript
function evolution_Scene(creatureCompositeIn){
    var ground;
    var circle;

    let creatureContainer = [];

    this.mySetup = function() {
        var canvas = createCanvas(800, 800);
        engine = Engine.create();
        world = engine.world;
        Matter.Runner.run(engine);

        engine.gravity.scale = 0.001;
        engine.gravity.y = 1;

        ground = new MyRect(400, 790, 800, 100, { isStatic: true });
        
        var canvasMouse = Mouse.create(canvas.elt);
        mConstraint = MouseConstraint.create(engine, { mouse: canvasMouse});
        Composite.add(world, mConstraint);
    }

    this.myDraw = function(){
        background(51);

        ground.show();
    }
```
{% endtab %}

{% tab title="creature_Creator.js" %}
<pre class="language-javascript"><code class="lang-javascript">this.mySetup = function() {
    //...//
<strong>    doneButton = createButton("Done");
</strong>    doneButton.mousePressed(doneButtonDown)
}

function doneButtonDown(){
    if(creatureComposite.bodies.length &#x3C;= 3 || creatureComposite.constraints.length &#x3C;=3){
        console.log("not enough joints / muscles")
        return;
    }
    jointButton.remove();
    muscleButton.remove();
    restartButton.remove();
    doneButton.remove();
    changeScene(1, creatureComposite);
}</code></pre>
{% endtab %}
{% endtabs %}

I now wanted to create an object for the creature so I could create and manage multiple at once, so I created a function in the function\_Bank.js script which would be assigned an ID and collision layer and then copy creatureCompositeIn (the creature the player created) and the array of render objects

```javascript
function MyCreature(McreatureID, McreatureComposite, McreatureColisionLayer, McreatureRenderer){ //we need to add options to this
    this.creatureID = McreatureID;
    this.creatureComposite = McreatureComposite;
    this.creatureColisionLayer = McreatureColisionLayer;
    this.creatureRenderer = McreatureRenderer;

    this.creatureSetup = function(){
        console.log(McreatureRenderer)

        for(let i = 0; i < McreatureComposite.bodies.length; i++){
            Composite.add(world, McreatureComposite.bodies[i]);
            McreatureComposite.bodies[i].collisionFilter = {category: McreatureColisionLayer, mask: 1 | McreatureColisionLayer}; //reativate later
        }
        Composite.add(world, creatureComposite);
    }

    this.show = function(){
        
        for (let i = 0; i< McreatureRenderer.length; i++){
            McreatureRenderer[i].show() //for each element in list render it
        }
    }
}
```

This didn't work due to a couple reasons I will state in the challenges section, so I approached the problem from a slightly different angle. Instead of copying the composite directly, I took the coordinates of the circles (joints) and created new objects under a new composite and rendering array unique to that one instance of a creature. I then did the same with the constraints (muscles), which where a bit harder because I had to get the 2 correct circles to attach it too. To do this I found how far down the list in the original each circle was and then get the circle that was corresponded to the same position.\
\
I then finally put the code into a nested function called creatureSetup() and created another nested function called show(), which similar to the simple objects from cycle 3, rendered a graphic for each physics object.

```javascript
function MyCreature(McreatureID, compositeIn, McreatureColisionLayer){
    let McreatureComposite;
    let McreatureRenderer = [];

    this.McreatureID = McreatureID; //these lines add the variables / objects to the console
    this.McreatureComposite = McreatureComposite;
    this.McreatureRenderer = McreatureRenderer;

    this.creatureSetup = function(){
        McreatureComposite = new Composite.create();
        Composite.add(world, McreatureComposite);
        for(let i = 0; i < compositeIn.bodies.length; i++){
            McreatureRenderer.push(new MyCircle(compositeIn.bodies[i].position.x, compositeIn.bodies[i].position.y, compositeIn.bodies[i].circleRadius));
        }
        for(let i = 0; i < compositeIn.constraints.length; i++){
            let j = 0;
            while (compositeIn.constraints[i].bodyA.id != compositeIn.bodies[j].id){
                j++;
            }
            let tempBodyA = McreatureComposite.bodies[j];
            j = 0;
            while (compositeIn.constraints[i].bodyB.id != compositeIn.bodies[j].id){
                j++;
            }
            let tempBodyB = McreatureComposite.bodies[j];

            McreatureRenderer.push(new MyConsraint(tempBodyA, tempBodyB, compositeIn.constraints[i].length, compositeIn.constraints[0].stiffness, 10, McreatureComposite))
        }
    }

    this.show = function(){     
        for (let i = 0; i< McreatureRenderer.length; i++){
            McreatureRenderer[i].show() //for each element in list render it
        }
    }
}
```

Finally, I added collision layer to the function, which was very easy to do due to my previous experimentation and implementation in cycle 4.

```javascript
McreatureRenderer.push(new MyCircle(compositeIn.bodies[i].position.x, 
    compositeIn.bodies[i].position.y, 
    compositeIn.bodies[i].circleRadius, 
    {collisionFilter: {category: McreatureColisionLayer,
    mask: 1 | McreatureColisionLayer}}, McreatureComposite));
```

And this is how the function is called from evolution\_Scene.js, creatureContainer is an array that contains all the creature objects for potential ease of use later on.

```javascript
for(let i = 0; i < 5; i++){
    creatureContainer.push(new MyCreature(i, creatureCompositeIn, 2**i))
    creatureContainer[i].creatureSetup();
}
```

### Challenges

Despite how simple these three objectives seem, each came with its own challenges and requirements. Due to this, I had to recreate the player creature for each new instance of the creature due to a couple issues with duplicating the original composite, such as matter.js having a bug where an object set to static couldn't simply be set to dynamic, and to do so would require a tough workaround. Even with this, the ID wouldn't change when I duplicated the object, causing the physics to presumably not work, but I also couldn't visually check due to the list of p5.js objects to render over the physics objects wasn't linking properly with the duplicates. It is because of this that I took a different approach with recreating the original 1:1.

## Testing

### Tests

| Test | Instructions                                                                          | What I expect                                                                   | What actually happens                                                         | Pass/Fail |
| ---- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- | --------- |
| 1    | Press the done button when under the required amount of objects.                      | The game to not change scenes and a message to appear in the console.           | The game does not change scenes and a message appears in the console.         | Pass.     |
| 2    | Create a viable creature and press done.                                              | The game to change to the evolution\_Scene.js                                   | The game changes to the evolution\_Scene.js.                                  | Pass.     |
| 3    | Create a viable creature and press done.                                              | Above and the creature to appear in the scene with physics and gravity applied. | Above and the creature appears in the scene with physics and gravity applied. | Pass.     |
| 4    | Change the amount of creatures created to 5, Create a viable creature and press done. | Above and 4 more creatures also appear and each have unique collision.          | Above and 4 more creatures also appear and each have unique collision.        | Pass.     |
| 5    | Change the amount of creatures created to 5, Create a viable creature and press done. | Above and 99 more creatures also appear and each have unique collision.         | Above and 99 more creatures also appear but some fall through the floor.      | Fail.     |

For test 5 I believe that the amount of collision layers in matter.js is capped so I will have a limit to how many creatures can be ran on screen.

### Evidence

<figure><img src="../.gitbook/assets/image (1) (4).png" alt=""><figcaption><p>Unviable creature, game doesn't let you further after pressing "done"</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (4).png" alt=""><figcaption><p>1 creature in evolution_Scene.js with gravity and physics.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>5 creatures in evolution_Scene.js with overlapping bounds and not colliding.</p></figcaption></figure>
