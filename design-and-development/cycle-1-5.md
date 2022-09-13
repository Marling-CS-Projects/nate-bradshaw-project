# 2.2.6 Cycle 6 - Creature Creator

## Overview

I am now focusing on the custom creature creation for the player. I have decided to keep it simple with restricting the building blocks to circles and constraints to act as "joints" and "muscles". I will then need buttons for the user to press to switch between each building element and a clear button to allow the user to restart if they want.

## Design

### Objectives&#x20;

* [x] Add buttons to the page
* [x] Add the ability to add circles on click
* [x] Add the ability to add constraints on click
* [x] Add the ability to clear the page on a button press

### Usability Features

To implement this, I need the script to have different uses for a mouse click, which I plan to use a switch statement which the different buttons will change for the mouse input to have different effects.

| Variable Name | Use                                                                                       |
| ------------- | ----------------------------------------------------------------------------------------- |
| jointButton   | Creates a default HTML button using matter js DOM so that the press activates a function. |
| muscleButton  | Creates a default HTML button using matter js DOM so that the press activates a function. |
| restartButton | Creates a default HTML button using matter js DOM so that the press activates a function. |
| myCreature    | The array of objects (circles and constraints) that have been placed by the player.       |
| switchCaseX   | The variable used in the switch statement to change what happens on click.                |

### Pseudocode

```javascript
Setup(){
    Matter.js.setup()

    createButton('Joint');
    onButtonDown(), switchCaseX = 0;

    createButton('Muscle');
    onButtonDown(), switchCaseX = 1;

    createButton("Restart");
    onButtonDown(), clear()
}

tMouseClicked(){

    switch(switchCaseX) {
        case 0:
            add circle();
            break;
        case 1:
            add constraint();
            break;
    }
}
```

## Development

### Outcome

I first focused on creating the buttons. To do this I needed to use p5.js with a Documentation Object Model. However, it turned out the online version of p5.js I was using didn't contain DOM, so I had to download it to reference locally to get the buttons to show. After I did this, I centred the buttons and put a manual offset to stack them underneath the game window. I then created functions for each being clicked

```javascript
let jointButton;
let muscleButton;
let restartButton;

Setup(){
    var canvasMouse = Mouse.create(canvas.elt);
    mConstraint = MouseConstraint.create(engine, { mouse: canvasMouse});
    World.add(engine.world, mConstraint);

    jointButton = createButton('Joint');
    jointButton.mousePressed(jointButtonDown);
    
    muscleButton = createButton('Muscle');
    muscleButton.mousePressed(muscleButtonDown);
    
    muscleButton.center('horizontal');
    muscleButton.position(muscleButton.position().x, muscleButton.position().y + 30);
    
    restartButton = createButton("Restart");
    restartButton.mousePressed(restartButtonDown)
    
    restartButton.center('horizontal');
    restartButton.position(restartButton.position().x, restartButton.position().y + 60);
}

Draw(){
    background(51);

    jointButton.center('horizontal');

    muscleButton.center('horizontal');

    restartButton.center('horizontal');
}

function jointButtonDown(){
    console.log("joint button pressed");
}

function muscleButtonDown(){
    console.log("muscle button pressed");
}

function restartButtonDown(){
    console.log("restart button pressed");
}  
```

After this, I added the switch statement, which would be changed by clicking on each button. For the restart button I wanted the restart to happen on the button press rather than the user having to click the button and then click again, as I believe it may be confusing.

Also during this, I found that mouse clicks outside of the canvas would still register, so I made a function called mouseInCanvas() to return a bool to tell the script weather the mouse was over the canvas when a click input was made. I put this function in the function\_Bank.js script.

{% tabs %}
{% tab title="creature_Creator.js" %}
```javascript
//...//

function jointButtonDown(){
    switchCaseX = 0;
    console.log("joint button pressed");
}

function muscleButtonDown(){
    switchCaseX = 1;
    console.log("muscle button pressed");
}

function restartButtonDown(){
    console.log("restart button pressed");
}  

MouseClicked(){
    if(mouseInCanvas(mouseX, mouseY, 400, 400)){
        switch(switchCaseX) {
            case 0:
                console.log("case 0");
                break;
            case 1:
                console.log("case 1");
                break;
            default:
                console.log("default");
        }
    }
}
```
{% endtab %}

{% tab title="function_Bank.js" %}
```javascript
function mouseInCanvas(x, y, canvasX, canvasY){
    if(x > canvasX || x < 0 || y > canvasY || y < 0){
        return false;
    }
    else{
        return true;
    }     
}
```
{% endtab %}
{% endtabs %}

This worked as expected. For the creation of circles, I added an array for all of the player made objects to go in and then using a previously made function in function\_Bank.js to implement the circle creation. The constraint was a bit more complex, as I needed two objects to place a constraint between and at the distance of those two objects. To do this I utilised the feature from matter.js's mouse constraint to get the body from clicking on an object. Using this, I could get the body and location of a first circle, store it in a variable, and then get the body and location of a second circle, calculate the distance and connect them with a constraint. To do this I added another function to function\_Bank.js, getDistance(). The logic for this would to click a first circle to be the start of the constraint and then to click a second circle to be the end of the constraint, connecting the two.

{% tabs %}
{% tab title="creature_Creator.js Circle" %}
```javascript
case 0:
    myCreature.push(new MyCircle(mouseX, mouseY, 15, { isStatic: true }));
    break;
```
{% endtab %}

{% tab title="creature_Creator.js Constraint" %}
```javascript
case 1:
    if(mConstraint.body != null){ //checking if the mouse even clicked an object
        if (temp == null){ //first circle
            temp = mConstraint.body;
        }
        else{ //second circle, distance and constraint      
            var distance = getDistance(temp.position.x, temp.position.y, mConstraint.body.position.x, mConstraint.body.position.y);
            myCreature.push(new MyConsraint(temp, mConstraint.body, distance, 0.4, 10));

            temp = null; //resetting for next constraint
        }
    }
    break;
```
{% endtab %}

{% tab title="function_Bank.js" %}
```javascript
//...//

function getDistance(x1, y1, x2, y2){
    let y = x2 - x1;
    let x = y2 - y1;
    
    return Math.sqrt(x * x + y * y);
}                case 1:
                    console.log("1");
                    if(mConstraint.body != null){
                        if (temp == null){
                            temp = mConstraint.body;
                            console.log(temp);
                            console.log("2");
                        }
                        else{
                            console.log("3");
                            
                            console.log(temp.position.x, temp.position.y);
                            console.log(mConstraint.body.position.x, mConstraint.body.position.y);
                            var distance = getDistance(temp.position.x, temp.position.y, mConstraint.body.position.x, mConstraint.body.position.y);
                            myCreature.push(new MyConsraint(temp, mConstraint.body, distance, 0.4, 10));
                
                            temp = null;
                        }                case 1:
                    console.log("1");
                    if(mConstraint.body != null){
                        if (temp == null){
                            temp = mConstraint.body;
                            console.log(temp);
                            console.log("2");
                        }
                        else{
                            console.log("3");
                            
                            console.log(temp.position.x, temp.position.y);
                            console.log(mConstraint.body.position.x, mConstraint.body.position.y);
                            var distance = getDistance(temp.position.x, temp.position.y, mConstraint.body.position.x, mConstraint.body.position.y);
                            myCreature.push(new MyConsraint(temp, mConstraint.body, distance, 0.4, 10));
                
                            temp = null;
                        }                case 1:
                    console.log("1");
                    if(mConstraint.body != null){
                        if (temp == null){
                            temp = mConstraint.body;
                            console.log(temp);
                            console.log("2");
                        }
                        else{
                            console.log("3");
                            
                            console.log(temp.position.x, temp.position.y);
                            console.log(mConstraint.body.position.x, mConstraint.body.position.y);
                            var distance = getDistance(temp.position.x, temp.position.y, mConstraint.body.position.x, mConstraint.body.position.y);
                            myCreature.push(new MyConsraint(temp, mConstraint.body, distance, 0.4, 10));
                
                            temp = null;
                        }
                    }
                    console.log("case 1");
                    break;
```
{% endtab %}
{% endtabs %}

Finally, the restart button needed to go though the array of objects placed by the player and remove them. I did this by going though each item on the array and then deleting its body before clearing the array to stop p5js rendering the objects, deleting them.

```javascript
function restartButtonDown(){
    for (let i = 0; i< myCreature.length; i++){
        Composite.remove(world, myCreature[i])      
    }
    myCreature = [];
}  
```

### Challenges

The main challenge for this cycle was getting the constraint to work, as it was difficult to get the right parts of the body put into the functions, but after a bit of trial and error alongside educated guesses, I managed to get the right information passed in.&#x20;

Furthermore, the first attempt at the restart button was bugged as I only removed the p5.js graphical representation by removing the items from the array that was drawing them, which caused the physics of these objects to still exist while invisible. I fixed this by adding a matter.js method for removing bodies from a composite  before clearing the array.

## Testing

### Tests

| Test | Instructions                                                                                                                | What I expect                                                         | What actually happens                                                            | Pass/Fail |
| ---- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------- |
| 1    | Clicking on the "Joint" button and placing a joint (circle) on canvas and off canvas.                                       | A joint to be placed at the click location and not placed off canvas. | The joint is placed on the canvas, but not when the mouse is outside the canvas. | Pass.     |
| 2    | Clicking on the "Muscle" button and on two joints to create a constraint between them.                                      | A constraint to be created.                                           | A constraint is created.                                                         | Pass.     |
| 3    | Placing multiple constraints coming from one joint.                                                                         | All constraints to be placed correctly.                               | All constraints are placed correctly.                                            | Pass.     |
| 4    | Place a joint, select it for the start of a constraint, restart, place another joint, click on it again in constraint mode. | Nothing to happen                                                     | Constraint is placed from empty space to the new joint                           | Fail.     |
| 5    | Press "Restart" button.                                                                                                     | All placed objects are removed.                                       | All placed objects are removed.                                                  | Pass      |

To fix the very specific case where a constrain can be made from fin air, I will be introducing a line of code in each other button press function to set "temp" (first constraint location) as null to avoid this.

```javascript
function jointButtonDown(){
    switchCaseX = 0;
    temp = null;
}

function muscleButtonDown(){
    switchCaseX = 1;
}

function restartButtonDown(){
    temp = null;
    for (let i = 0; i< myCreature.length; i++){
        Composite.remove(world, myCreature[i])       
    }
    myCreature = [];
}  
```

| Test | Instructions                                                                                                                | What I expect     | What actually happens | Pass/Fail |
| ---- | --------------------------------------------------------------------------------------------------------------------------- | ----------------- | --------------------- | --------- |
| 1    | Place a joint, select it for the start of a constraint, restart, place another joint, click on it again in constraint mode. | Nothing to happen | Nothing happens.      | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (6) (2).png" alt=""><figcaption><p>Button Placement</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>Buttons location staying the same when the window is resized</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption><p>Joint placement</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (3).png" alt=""><figcaption><p>Constraint placement</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (3).png" alt=""><figcaption><p>Restart button</p></figcaption></figure>
