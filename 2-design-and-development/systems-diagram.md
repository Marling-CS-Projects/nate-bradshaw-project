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
*

### Error Tolerant

The solution should have as few errors as possible and if one does occur, it should be able to correct itself. To do this, I will write my code to manage as many different game scenarios as possible so that it will not crash when someone is playing it.

#### Aims

* The game doesn't crash
* The game does not contain any bugs that damage the user experience

### Easy To Learn

The solution should be easy to use and not be over complicated. To do this, I will create simple controls for the game. I will make sure that no more controls are added than are needed in order to keep them as simple as possible for the players.

#### Aims

* Create a list of controls for the game
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
