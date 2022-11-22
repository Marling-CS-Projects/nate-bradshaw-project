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
creatureContainer[selectedCreature].draw();</code></pre>

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

This task was relatively easy due to the creature object already having ID's from 0 to 31, matching their position in the creatureContainer\[] array. All I had to do was add the buttons and a variable they could increase / decrease that could pull one creature from the array.&#x20;

### Time Control: Research and Testing

Starting with researching and testing, I quickly found that matter.js simulation speed control got quite buggy, especially when speeding up, with bodies falling through each other and off the screen.

### Time Control: Outcome

Starting with researching and testing, I quickly found that matter.js simulation speed control got quite buggy, especially&#x20;

### Challenges

Due to the simplicity and small scale of this cycle, there wasn't much difficulty to it.

## Testing

### Tests

| Test | Instructions                                                                                    | What I expect                                        | What actually happens                                | Pass/Fail |
| ---- | ----------------------------------------------------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------- | --------- |
| 1    | Check all added html elements are present on both pages.                                        | All added elements are present where they should be. | All added elements are present where they should be. | Pass.     |
| 2    | Check the GitHub Pages hosted webpage has the same functionality as the locally hosted webpage. | All functionality and libraries / code is present.   | All functionality and libraries / code is present.   | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>index.html</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>game_page.html</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (2).png" alt=""><figcaption><p>Favicon</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (3).png" alt=""><figcaption><p>Website hosted on GitHub Pages</p></figcaption></figure>
