# 2.1 Design Frame

## Systems Diagram

![](<../.gitbook/assets/image (1).png>)

This diagram shows the main focuses of my project and their sub focuses. Through development, I will focus on different sub sections that fit in with my development focus for the cycle I'm on. I broke down the project like this to show fit the success criteria and show how I wish to decompose the features of my project into more manageable smaller topics.

## Usability Features

Usability is important for my project as it is aimed at schoolchildren, so the usability features need to be implemented correctly to ensure the best experience for the user.

### Effective

I will need to make the goal and improvement of the agents clear over the generations to keep the player informed and engaged with the objective fore the agents

#### Aims

* Create clear indicators for the agent ai improvements
* Keep the user engaged with the agents improvements

### Efficiency

For user efficiency, the UI design needs to be as intuitive and respond quickly to user inputs. The options also need to be reachable without being hidden behind too many menus or options.

#### Aims

* Create a easy, readable and responsive UI

### Engaging

To keep the user engaged, I feel that a customisable stet of starting rules and environments would make the simulation feel more personalised. I also feel like speed controls would be needed for the user to skip over any potentially boring part of the machine learning.

#### Aims

* Create a variety of start conditions for the player to customise
* Include speed controls whilst keeping the integrity of the simulation

### Error Tolerant

The systems that I will need to design will be the agents and UI input handling. If designed correctly, due to the lack of direct player control should also reduce unexpected errors. I do still have to make error handling to make sure the simulation doesn't crash in runtime.&#x20;

#### Aims

* The game doesn't crash
* Try to remove as many bugs as I can

### Easy To Learn

The simulation needs to have either popups or a tutorial to fully explain to the user their extent of interaction and what their interactions do.

#### Aims

* Create a tutorial scene
* Create an in-level guide that helps players learn how to play the game

## Pseudocode for the Game

### Pseudocode for game

This is the basic layout of the object to store the details of the game. This will be what is rendered as it will inherit all important code for the scenes.

```
object Game
    type: Phaser
    parent: id of HTML element
    width: width
    height: height
    physics: set up for physics
    scenes: add all menus, levels and other scenes
end object

render Game to HTML web page
```

### Pseudocode for a level

This shows the basic layout of code for a Phaser scene. It shows where each task will be executed.

```
class Level extends Phaser Scene

    procedure preload
        load all sprites and music
    end procedure
    
    procedure create
        start music
        draw background
        create players
        create platforms
        create puzzle elements
        create enemies
        create obstacles
        create finishing position
        create key bindings
    end procedure
    
    procedure update
        handle key presses
        move player
        move interactable objects
        update animations
        check if player at exit
    end procedure
    
end class
```
