# 2.2.12 Cycle 12 - Adjustments and Visual Clarity

## Overview

In this cycle I will refine the creatures to create a more interesting evolution to watch and also make some visual changes to make the process easier to follow.

## Design

### Objectives&#x20;

* [x] Adjust creature parameters to make more interesting movement.
* [x] Create a striped background for the user to recognise movement against.
* [x] Highlight the leading creature
* [x] Make all creatures other than the leading creature transparent

### Usability Features



| Variable Name  | Use                                                                                                                                               |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| stiffness      | The stiffness value for a constraint passed into the MyConstraint function.                                                                       |
| normaliseInput | A function to take a value, start and end of a range and return a value between 0 - 1 to be passed into the neural network to standardise inputs. |
| firstBestID    | Holds the id of the creature in first place.                                                                                                      |

### Pseudocode

```javascript
drawStripedBackground();

for(each in creatureContainer){
    if (creatureContainer.ID != firstBestID){
        MakeTransparent();
    }
    else{
        MakeGreen();
    }
}
```

## Development

### Outcome

The development of this cycle was rather strait forward compared to previous cycles, as all new features and adjustments slot in nicely.  Firstly, the striped background was as simple as creating a for loop that created alternating colours of p5.js boxes.

```javascript
for (let i = 0; i < 100; i++){
  if (i % 2 == 0) {
    fill(70); //sets the fill for everything below it
  }
  else {
    fill(51);
  }
  rect(-500 + (200 * i), -4000, 200, 5000);
}
```

Secondly, due to the program already knowing the best creature of the generation, implementing a system to change the colour of the leading creature and reduce the opacity of the rest was also rather simple.

```javascript
//(r, g, b, a), 0 -> 225, so this is ~30% opacity white fill
fill(225, 225, 225, 70)
//(r, g, b, a), 0 -> 225, so this is ~30% opacity black outline
stroke(0, 0, 0, 70)
for (let i = 0; i < creatureContainer.length; i++) { //all the creatures but first
  if(firstBestID != creatureContainer[i].McreatureID){
    creatureContainer[i].show();
    creatureContainer[i].think();
  }
}

stroke(0, 0, 0, 225) //now 100% oppacity black outlines

if(firstBestID != null){
  fill(0, 225, 0, 225) //green fill for leading creature
  creatureContainer[firstBestID].show();
  creatureContainer[firstBestID].think();
}
```

Finally, changing the logic of the creature, I decided it would be better to normalise the inputs and allow multiple muscles to be moved at once. I also adjusted the speed the muscles can expand / contract at and the stiffness of the constraints. To accommodate the neural network needing to now allow for multiple muscles moving at once, I changed the activation function from "softmax" (which normalises all the neural network outputs to add together to 1) to "sigmoid" (which just normalises each output to between 0 and 1).

<pre class="language-javascript"><code class="lang-javascript">function normaliseInput(value, min, max, destMin = 0, destMax = 1,) {
  return destMin + ((value - min) / (max - min)) * (destMax - destMin);
}
<strong>
</strong><strong>let inputs = [];
</strong>
for (let i = 0; i &#x3C; McreatureComposite.constraints.length; i++) {
  let minVal = 35;
  if (compositeIn.constraints[i].length - 200 > 40) {
    minVal = compositeIn.constraints[i].length - 200;
  }
  inputs[i] = normaliseInput(McreatureComposite.constraints[i].length, compositeIn.constraints[i].length + 200, minVal);
}

let outputs = this.brain.predict(inputs);

for(let i = 0; i &#x3C; outputs.length; i++){ //for each constraint, so that multiple can move at once
//0.45 &#x3C;= x >= 0.55 = no movement, below is shrinking, above is growing
  if(outputs[i] &#x3C; 0.45 &#x26;&#x26; McreatureComposite.constraints[i].length > 40 &#x26;&#x26; McreatureComposite.constraints[i].length >= compositeIn.constraints[i].length - 200) {
    McreatureComposite.constraints[i].length -= 5 * (0.5 - outputs[i]);
  }
  else if (outputs [i] > 0.55 &#x26;&#x26; McreatureComposite.constraints[i].length &#x3C;= compositeIn.constraints[i].length + 200) {
    McreatureComposite.constraints[i].length += 5 * (0.5 - (outputs[i] - 0.5));
  }
}</code></pre>

### Challenges

The main challenge with this cycle was fine tuning the stiffness of the constraints, as if they were too stiff the creatures would start spinning (which was fun to watch and got them the furthest they have gotten but was unfortunately unwanted) and if they were too loose the creature would resemble jelly more than anything else and not do anything partially well. After a lot of trial and error, a value of 0.01 was an acceptable in-between.&#x20;

## Testing

### Tests

| Test | Instructions                                                                                                                                             | What I expect       | What actually happens | Pass/Fail |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | --------------------- | --------- |
| 1    | Observe a static striped background, the lead creature being green, all other creatures being transparent and creatures moving multiple muscles at once. | All to be observed. | All are observed.     | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Demonstration of better average x without best peak x</p></figcaption></figure>
