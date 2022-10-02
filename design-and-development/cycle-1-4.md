# 2.2.5 Cycle 5 - Scenes and Scene Logic

## Overview

The focus of this cycle is to structure the scripts so they are called by a scene manager script, allowing me to switch between scenes easily and to keep the scripts for each scene separated to make developing easier. Any generic functions can be put in the "function bank" to be called from anywhere.

## Design

### Objectives&#x20;

* [x] Create a scene manager script
* [x] Have a scene script be run through the scene manager script
* [x] Be able to trigger input events
* [x] Be able to switch scenes

### Usability Features

To implement this, I had to change the current scripts to be contained within a function that can be called based on the sceneIndex. This includes putting the contents of setup() and draw() as nested functions, mySetup() and myDraw() to be called from the scene manager script. I further had to implement a nested function for p5 inputs (e.g. myMouseClicked()) and once again have it called from the scene manger script.

| Variable Name | Use                            |
| ------------- | ------------------------------ |
| sceneIndex    | Keeps the current scene number |

### Pseudocode

The idea is for the scene manager script to be able to control what scene is currently being shown and switch between scenes.

```
sceneIndex = 0;

if sceneIndex = 0
    scene1.setup()
else if sceneIndex = 1
    scene2.setup()
    
if mouseClicked
    nextScene()
    
function draw()
    currentScene.draw()
```

## Development

### Outcome

The scene manager script currently can select a scene based on sceneIndex and then setup and draw that scene while being able to switch to another scene.

```javascript
var sceneIndex = 0;

//inital scene, for testing to allow me to change 1 variable to test a differnt
//scene
if (sceneIndex == 0){
    creature_Creator();
}
else if (sceneIndex == 1){
    matter_and_p5_test();
}

//calling the setup for the current scene, only for the intial scene
function setup() {
  mySetup();
}

//draw is called each frame, this is displaying the current scene
function draw(){
  myDraw();
}

//to hadle inputs for p5, the p5 function needs to be called here I've found
//in future I want to change this to be more generic for any input
function mouseClicked(){
  myMouseClicked();
  if (sceneIndex == 0){
    creature_Creator();
    mySetup();
  }
  else if (sceneIndex == 1){
    matter_and_p5_test();
    mySetup();
  }
}
```

For the scripts themselves, this is the layout of the nested functions. Also to be noted currently each scene contains a nested function that allows it to switch to the next script.

```javascript
function creature_Creator(){
    this.mySetup = function() {
        //setup function
    }

    this.myDraw = function(){
        //draw function
    }

    this.myMouseClicked = function(){
        sceneIndex += 1;
      }
}
```

### Challenges

One of the main challenges I faced was trying to get the scene manager to correctly setup() a new scene. This was because I overcomplicated it, believing that the mySetup() nested function should only be called in the scene managers setup() function. This was not needed, and as shown above, the setup can be called when the new scene is switched before the draw() is called on the next frame. I also had some confusion about variable scope for the scenes themselves being able to alter the sceneIndex variable, but the scope didn't pose an issue in the end.

## Testing

### Tests

| Test | Instructions                                                                           | What I expect                            | What actually happens          | Pass/Fail |
| ---- | -------------------------------------------------------------------------------------- | ---------------------------------------- | ------------------------------ | --------- |
| 1    | Have a scene loaded from the scene manager.                                            | The scene to show as normal.             | The scene displayed as normal. | Pass.     |
| 2    | With the code in the scenes themselves, be able to change sceneIndex and switch scene. | On input, the next scene is switched to. | The next scene is switched to. | Pass.     |
| 3    | Have the click to create cube function work from the scene manager script.             | A cube is created on click.              | The scene reloads              | Fail.     |

Due to an oversight, whenever there is an input made in the scene manager script, it also checks what the current scene is and reloads. To fix this, I am going to add a function that the scenes can call to make the scene manager script change scenes.

```javascript
//old scene_Manager.js code

function mouseClicked(){
  myMouseClicked();
  if (sceneIndex == 0){
    creature_Creator();
    mySetup();
  }
  else if (sceneIndex == 1){
    matter_and_p5_test();
    mySetup();
  }
}

//new scene_Manager.js code

//this now only handles mouse inputs
function mouseClicked(){
  myMouseClicked();

//this can now be better implimeted into the scene scripts
function changeScene(newSceneIndex){
  sceneIndex = newSceneIndex;
  if (sceneIndex == 0){
    creature_Creator();
    mySetup();
  }
  else if (sceneIndex == 1){
    matter_and_p5_test();
    mySetup();
  }
}

//use of changeScene():
this.myMouseClicked = function(){
    changeScene(1)
  }
```

## Testing 2

### Tests

| Test | Instructions                                                               | What I expect                            | What actually happens          | Pass/Fail |
| ---- | -------------------------------------------------------------------------- | ---------------------------------------- | ------------------------------ | --------- |
| 1    | Call the changeScene() function from the scene                             | On input, the next scene is switched to. | The next scene is switched to. | Pass.     |
| 2    | Have the click to create cube function work from the scene manager script. | A cube is created on click.              | A cube is created on click.    | Pass.     |

### Evidence

![First scene loaded.](<../.gitbook/assets/image (10) (1).png>)

![Second scene loaded in runtime.](<../.gitbook/assets/image (2) (2).png>)

![Inputs being handled through the scene manager](<../.gitbook/assets/image (1) (1) (3).png>)
