# 2.1 Design Frame

## Systems Diagram

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

* Create a easy to use UI
* Make the creature setup straight forward

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

* Create a tutorial
* Keep the design, visually and logically, simple and easy to understand&#x20;

## Pseudocode for the Simulation

### Design Pseudocode

This is the pseudocode for how the menu and simulation logic could be constructed and flow.

```
<script src="phaserGame.js"></script>

---

var config{
    physics: Matter{}
}

preload{
    ground_Sprites;
    UI_Sprites;
}

create(){
    TitleScreen();
    CreatureCreation();
    RunSimulation();
}

update(){
    RunNeuralNetwork();
    CameraControls();
    UIControls();
    SimulationTracker(): //to log which agent preformed the best
}
```

### Machine Learning Pseudocode

This is a basic look at how I would plan to implement the agents within the simulation and the features I would give them as an object.

```
object Agent{
    let randomNum(0-1);

    if generation == 1
        GenerateNeuralNetwork(randomNum);
    else 
        InheritNeuralNetwork(randomNum, generation);
    
    RandomiseBody(randomNum, generation);
}
```
