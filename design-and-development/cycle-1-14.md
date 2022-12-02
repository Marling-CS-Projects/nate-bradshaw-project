# 2.2.15 Cycle 15 - User Interaction

## Overview

Within this cycle I want to focus on user interaction within the simulation by improving the ability to view, by letting the user highlight a creature, and to adjust the speed of the simulation to let the user either speed through earlier generations or slow down and see a generation in more detail.

## Design

### Objectives&#x20;

* [x] Add buttons to allow the user to select the next or previous creature
* [x] Add a different colour for the selected creature and render it over the other creatures
* [x] Add text to tell the user information about the selected creature
* [x] Successfully test time control
* [x] Add time control slider
* [x] Add text for the user to know the current time multiplier
* [x] Make neural network muscle movement speed match the time control
* [x] Make the timer slow down / speed up to match the time control

### Usability Features

| Variable Name          | Use                                                                                      |
| ---------------------- | ---------------------------------------------------------------------------------------- |
| creatureSelectedID     | The current creature that the user is viewing, used to render that creature differently. |
| nextCreatureButton     | The button that allows the user to cycle forward through the creatures.                  |
| previousCreatureButton | The button that allows the user to cycle backward through the creatures.                 |
| currentTimeScale       | The current time scaling for the simulation.                                             |
| timeScaleSlider        | The slider that allows the user to change the time scale of the simulation.              |

### Pseudocode

<pre class="language-javascript"><code class="lang-javascript"><strong>let selectedCreature = 0;
</strong><strong>
</strong><strong>let nextCreature = new button;
</strong><strong>let lastCreature = new button;
</strong>
if nextCreature.Pressed:
    selectedCreature++;
end if
    
if lastCreature.Pressed:
    selectedCreature++;
end if
    
//after the main draw for all the other creatures
drawCol = blue;
creatureContainer[selectedCreature].draw();
</code></pre>

```javascript
let slider = createSlider(1, 5); //1x speed to 5x speed
setSimulationSpeed(slider.value);
setTimerSpeed(slider.value);

for each creture in creatureContainer[]:
    think(timescale = slider.value);
end for
```

## Development

### Creature Selection: Outcome

This task was relatively easy due to the creature object already having ID's from 0 to 31, matching their position in the creatureContainer\[] array. All I had to do was add the buttons and a variable they could increase / decrease that could find and render one creature from the array.&#x20;

<pre class="language-javascript"><code class="lang-javascript"><strong>let nextCreatureButton;
</strong><strong>let previousCreatureButton;
</strong><strong>let creatureSelectedID = 0;
</strong><strong>
</strong><strong>this.mySetup = function () {
</strong>    //...//
<strong>  nextCreatureButton = createButton('View Next Creature');
</strong>  nextCreatureButton.mousePressed(nextCreatureButtonDown);
  
  nextCreatureButton.center('horizontal');
  nextCreatureButton.position(nextCreatureButton.position().x,
  nextCreatureButton.position().y + 20);
  
  previousCreatureButton = createButton('View Previous Creature');
  previousCreatureButton.mousePressed(previousCreatureButtonDown);
  
  previousCreatureButton.center('horizontal');
  previousCreatureButton.position(previousCreatureButton.position().x,
  previousCreatureButton.position().y + 50);
    //...//
}

this.myDraw = function(){
    //...//
  fill(0, 0, 225, 225); //blue
  creatureContainer[creatureSelectedID].show();
    //...//
}

function previousCreatureButtonDown() {
  if (creatureSelectedID == 0){
    creatureSelectedID = 31;
  }
  else{
    creatureSelectedID--;
  }
}

function nextCreatureButtonDown() {
  if (creatureSelectedID == 31){
    creatureSelectedID = 0;
  }
  else{
    creatureSelectedID++;
  }
}
</code></pre>

<figure><img src="../.gitbook/assets/image (6) (4).png" alt=""><figcaption><p>The buttons cycle through and the creature in blue is the one selected.</p></figcaption></figure>

I also added some text to tell the user what creature has been highlighted and its current position. I  also added an alternative line of text for when the peak y needs to be displayed.

```javascript
if(optionsIndex != 2){
  //...//
  text(("Viewing Creature " + (creatureSelectedID + 1) + ", Average X at: " + parseInt(creatureContainer[creatureSelectedID].averageX)), 0, 102)
}
else{
  //...//
  let num2 = (parseInt((creatureContainer[creatureSelectedID].bestY - startingPos)) * -1) - 999999150 //looks awkward but is needed
  text(("Viewing Creature " + (creatureSelectedID + 1) + ", Current Peak Y at: " + num2), 0, 102)
}
```

### Time Control: Research and Testing

Moving onto the time control, during the researching and testing, I quickly found that matter.js simulation speed control (engine.timing.timeScale) got quite buggy, especially when speeding up, with bodies falling through each other and off the screen. I found that a time multiplier range of 0.1 to 1.3 was suitable, as the simulation stayed stable within these values.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>Time scale set to 2 breaking the simulation.</p></figcaption></figure>

### Time Control: Outcome

To allow the user to interact with the time, I created a slider limited between 0.1 and 1.3 that would be used as the time scale multiplier. Using this I set the engines time scale and added a variable to the neural network trough .think() so the rate of neural network movement would match the timescale, with the goal of keeping the simulation accurate through different time scaling. I then after adjusted the timer of the generation to update to the new time each decisecond (1/10th of a second).

{% tabs %}
{% tab title="evolution_Scene.js" %}
```javascript
let currentTimeScale = 1;
let timerStartedCount = false;

this.mySetup = function(){
        //...//
    timeScaleSlider = createSlider(0.1, 1.3, 1, 0.1);
    timeScaleSlider.center('horizontal');
        //...//
}

this.myDraw = function(){
        //...//
    currentTimeScale = timeScaleSlider.value();
    
    engine.timing.timeScale = currentTimeScale;
        //...//
        
        //...//       
    fill(225, 225, 225, 70);
    stroke(0, 0, 0, 70);
    for (let i = 0; i < creatureContainer.length; i++) {
      if(firstBestID != creatureContainer[i].McreatureID || creatureSelectedID != creatureContainer[i].McreatureID){
          creatureContainer[i].show();
          creatureContainer[i].think(currentTimeScale);
      }
    }

    stroke(0, 0, 0, 225);
    fill(0, 0, 225, 225);
    creatureContainer[creatureSelectedID].show();
    creatureContainer[creatureSelectedID].think(currentTimeScale);

    if(firstBestID != null && firstBestID != creatureSelectedID){
      fill(0, 225, 0, 225);
      creatureContainer[firstBestID].show();
      creatureContainer[firstBestID].think(currentTimeScale);
    }
        //...//
    if (timeCount <= 0) {
      nextGen()
      startingPos = null;

      clearInterval(timerInterval);
      timerStartedCount = false;
      timeCount = time / 1000
    }

    if(!timerStartedCount){
      timerInterval = setInterval(setTime, (100 / currentTimeScale));
      timerStartedCount = true;
    }
}

function setTime(){
    timeCount -= 0.1;
    clearInterval(timerInterval);
    timerStartedCount = false;
}
```
{% endtab %}

{% tab title="function_Bank.js" %}
```javascript
this.think = function (timeScale = 1) {
  let inputs = [];

  for (let i = 0; i < McreatureComposite.constraints.length; i++) {
    let minVal = 35;
    if (compositeIn.constraints[i].length - 200 > 40) {
      minVal = compositeIn.constraints[i].length - 200;
    }
    inputs[i] = normaliseInput(McreatureComposite.constraints[i].length, compositeIn.constraints[i].length + 200, minVal);
  }

  let outputs = this.brain.predict(inputs);

  for(let i = 0; i < outputs.length; i++){
    if(outputs[i] < 0.45 && McreatureComposite.constraints[i].length > 40 && McreatureComposite.constraints[i].length >= compositeIn.constraints[i].length - 200) {//0.45 <= x >= 0.55 = no movement, below is shrinking, above is growing
      McreatureComposite.constraints[i].length -= 5 * (0.5 - outputs[i]) * timeScale;
    }
    else if (outputs [i] > 0.55 && McreatureComposite.constraints[i].length <= compositeIn.constraints[i].length + 200) {
      McreatureComposite.constraints[i].length += 5 * (0.5 - (outputs[i] - 0.5)) * timeScale;
    }
  }
}
```
{% endtab %}
{% endtabs %}

After this, I then added some more text to the canvas to give the user feedback on the time left in the generation and the timescale they were using.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>The timer counts down faster / slower proportional to the timescale.</p></figcaption></figure>

```javascript
text(("Time: " + (timeCount).toFixed(1)), 0, 132)
text(("Current Time Scale: " + currentTimeScale), 0, 162)
```

### Challenges

The main challenge with this cycle was getting the timer to adjust, as before I was using setTimeout() to wait the whole cycles length, so I had to adapt this to show a decreasing timer and adapt to the timescale. Th biggest problem was that I was lacking the clearInterval(), so the timer would speed up constantly.

## Testing

### Tests

| Test | Instructions                                                                               | What I expect                                                                    | What actually happens                                                                                    | Pass/Fail                          |
| ---- | ------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ---------------------------------- |
| 1    | Test the creature highlight with all creature objectives.                                  | No difference between "move to left" and "obstacles", current peak y for "jump". | As expected.                                                                                             | Pass.                              |
| 2    | Test the interaction of having the highlighted creature and first creature being the same. | The creature is the last colour rendered is used (blue).                         | No visual difference, but I noticed that the neural network was being activate twice due to the overlap. | Pass. (fixed with an if statement) |
| 3    | Observe the timer speeding up / slowing based on the time scale.                           | The timer speed to change proportionally to the time scale.                      | As expected.                                                                                             | Pass.                              |
| 4    | Move the time scale rapidly from side to side.                                             | Nothing significant.                                                             | The physic engine can't handle the rapid time scale change and the creatures break.                      | Fail.                              |

Due to test 4's fail, I made the time scale only update at the start of a generation, allowing the user to still interact with this system without breaking the simulation.

### Evidence

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption><p>1.3x time scale working with a 1st and separate selected creature</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>No issues with 1st and selected being the same.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (5).png" alt=""><figcaption><p>0.1x time scale working and alternative text for "jump".</p></figcaption></figure>
