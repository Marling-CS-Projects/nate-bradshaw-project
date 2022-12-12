# 2.2.16 Cycle 16 - Leader board

## Overview

In this final cycle I will add a leader board to allow the user to see the position of all the creatures at once and change the blue select to show the creatures in this order.

## Design

### Objectives&#x20;

* [x] Sort the creatures based on their average x / best y to get a full leader board
* [x] Change the selection feature to cycle through the leader board
* [x] Add a visual representation of the leader board

### Usability Features

| Variable Name             | Use                                                             |
| ------------------------- | --------------------------------------------------------------- |
| proxyCreatureContainer\[] | Holds the position of the creatures in the leader board         |
| leaderboard\[]            | Holds the DOM text for displaying the leader board to the user. |

### Pseudocode

```javascript
creatureContainer.sort(creatureContainer[].averageX)
//the cycling should work with a solution like this

for (let i = 0; i < creatureNum; i++;){ //text to show each creature and its place
    text(i, " place: ", creatureContainer[i].ID)
}
```

## Development

### Outcome

To start the implementation, I found a function that sorts an array by one element and made it sort in a descending order. I decided that I would create a new array which had the creature ID and average x or peak y, based on which one is needed. I did this to avoid potentially breaking something else with the code alongside not having to directly shuffle quite large objects around each frame. The proxy ID would keep track of the creature it was holding the place of and the comparison value would hold the average x or peak y and would be used to order the array, with the place in the array being the place on the leader board.\
\
Each frame the proxy array would be updated with the new values and sorted to have the correct leader board for that frame.

{% tabs %}
{% tab title="evolution_Scene.js" %}
```javascript
this.mySetup = function(){
    //...//
  for (let i = 0; i < creatureNum; i++) { //32 differnt collision layers is max due to bitmask, so thats 32 different creature limit
    creatureContainer.push(new MyCreature(i, creatureCompositeIn, 2 ** i))
    creatureContainer[i].creatureSetup();
    Composite.add(world, creatureContainer[i].McreatureComposite);
  
    proxyCreatureContainer.push(new MyCreatureProxy(i))//index is the same as the ID
  }
    //...//
}

this.myDraw = function(){
    //...//
  if (optionsIndex != 2){
    for (let i = 0; i < creatureNum; i++) { //for average x
      proxyCreatureContainer[i].comparisonValue = creatureContainer[proxyCreatureContainer[i].proxyID].averageX;
    }
  }
  else{
    for (let i = 0; i < creatureNum; i++) { //for peak y
      proxyCreatureContainer[i].comparisonValue = creatureContainer[proxyCreatureContainer[i].proxyID].bestY;
    }
  }

  proxyCreatureContainer.sort((a, b) => { //ordering the list
    if (optionsIndex != 2){
      return b.comparisonValue - a.comparisonValue;
    }
    else{
      return a.comparisonValue - b.comparisonValue;
      //this is reverse because y coords are inverted in the canvas.
    }
  });
    //...//
}
```
{% endtab %}

{% tab title="function_Bank.js" %}
```javascript
function MyCreatureProxy(proxyID, comparisonValue = 0) {
  this.proxyID = proxyID
  this.comparisonValue = comparisonValue
}
```
{% endtab %}
{% endtabs %}

This now being functional means that Cycle 10's and 13's functions for finding the best creature are obsolete and are replaced with this new sorted array, as it holds all the positions.

To make the selected creature cycle through the leader board, I changed how it selects by taking the place in the proxy array, getting the ID and using that ID (which correlates to the position of the selected creature in the container array) to render that creature differently. This logic of getting the ID from the proxy list based on the creatures place is now used everywhere else that used to use the bestX or bestY variables.

```javascript
fill(0, 0, 225, 225) //selected
creatureContainer[proxyCreatureContainer[creatureSelectedPlace].proxyID].show()
creatureContainer[proxyCreatureContainer[creatureSelectedPlace].proxyID].think(currentTimeScale);
```

Finally, implementing a visual leader board for the user to gawk at. When implementing this, I decided that there wasn't enough space on the canvas for a large amount of text, so using the p5.js functions with html DOM, I created the list outside the right side of the canvas. Due to having to centre the element, the text was also aligned to the centre, so I found a function that would pad zeros before the numbers to keep the text inline.

```javascript
let leaderboard = []; //using an array for easy access later on

this.mySetup = function () {
  for (let i = 0; i < creatureNum; i++){ //base setup for the text
    leaderboard.push(createElement('h5', (i + 1) + "text"))
    leaderboard[i].style('color', '#000000');
    leaderboard[i].center('vertical')
    leaderboard[i].center('horizontal')
    leaderboard[i].position(leaderboard[i].position().x + 510, leaderboard[i].position().y - (390 - 20 * i))
  }
    //...//
}

this.myDraw = function () {
    //...//
  for (let i = 0; i < creatureNum; i++){
    if(optionsIndex != 2){
      leaderboard[i].elt.firstChild.data = (padLeadingZeros(i + 1, 2) + ", Creature: " + padLeadingZeros((proxyCreatureContainer[i].proxyID + 1), 2) + ", At: "  + padLeadingZeros(parseInt(creatureContainer[proxyCreatureContainer[i].proxyID].averageX - startingPos), 4))
    }
    else{
      leaderboard[i].elt.firstChild.data = (padLeadingZeros(i + 1, 2) + ", Creature: " + padLeadingZeros((proxyCreatureContainer[i].proxyID + 1), 2) + ", At: "  + padLeadingZeros(parseInt((proxyCreatureContainer[i].comparisonValue * -1) + 800), 4))
    }
    leaderboard[i].center('vertical')
    leaderboard[i].center('horizontal')
    leaderboard[i].position(leaderboard[i].position().x + 510, leaderboard[i].position().y - (390 - 20 * i))
  }
    //...//
}

function padLeadingZeros(num, size) {
  var s = num+"";
  while (s.length < size) s = "0" + s;
  return s;
}
```

### Challenges

The main challenge with this cycle was making sure I dint break anything whilst implementing the new system for tracking the creatures places. For example, after I got the array working with the rest of the code, I noticed that the creatures stopped improving at all, which was due to passing the whole proxy array into the mutate() function instead of the ID from the array. Going through and fixing these invisible bugs without breaking something else posed a bit of a challenge.

## Testing

### Tests

| Test | Instructions                                                                  | What I expect                                                        | What actually happens                                                                                | Pass/Fail |
| ---- | ----------------------------------------------------------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | --------- |
| 1    | Observe the leader board and compare it to the best x on the canvas.          | The top of the leader board and best x on the canvas to be the same. | As expected.                                                                                         | Pass.     |
| 2    | Cycle through the creatures and check the order compared to the leader board. | The selection to move down the places in the leader board.           | As expected.                                                                                         | Pass.     |
| 3    | Resize the page to check the leader board texts position                      | The text to stay anchored to the right of the canvas.                | As expected, but the text line breaks with a small window and becomes unreadable (a very edge case). | Pass.     |

### Evidence

<figure><img src="../.gitbook/assets/image (3) (4).png" alt=""><figcaption><p>Leader board working, showing each creatures place and average X in descending order.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (5).png" alt=""><figcaption><p>Peak Y leader board working alongside separate peak Y line for selected creature.</p></figcaption></figure>
