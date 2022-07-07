# 2.2.1 Cycle 1 - Setting up

## Overview

For the first development cycle, I wanted to set up the development environment with phaser.io and matter.js within visual studio and test it with a local host and a modified phaser example scene.

## Design

### Objectives&#x20;

* [x] Get a local web host working
* [x] Set up a basic webpage
* [x] Implement Phaser.io into the webpage
* [x] Implement Matter.js within Phaser.io
* [x] Get the Phaser.io demo working
* [x] Modify the demo to test a future feature

### Usability Features

* Working href links on the webpage
* Good contrast on the website for readability
* Large game window size

The phaser.io example I found was a chain drawing example using the mouse and matter.js. I took this and used constraint modification to remove any previous constraints on a new constraints creation.

| Variable Name | Use                                                                                   |
| ------------- | ------------------------------------------------------------------------------------- |
| current       | Most recent shape created by the mouse.                                               |
| previous      | 2nd most recent shape created by the mouse.                                           |
| tempConst     | The current Constraint                                                                |
| lastPosition  | A Vector2 that keeps track of the last shape created by the pointer.                  |
| pinOptions    | A pre set range of settings that can be changed in one place to affect all instances. |

### Pseudocode

```
var config = {
    ---
}

function create(){
    var current;
    var pevious;
    var tempConst;
    
    var lastPosition = vector2();
    
    var pinOptions = {options}
    
    OnClick{
        lastPosition = mousePosition;
        
        matter.add.polygon(mousePosition, PinOptions);
    }
    
OnDrag{
    if (mousePosition isFarFrom lastPosition){
        current = matter.add.polygon(mousePosition, PinOptions);
        
        if (tempConst != null)
            delete tempconst;
            
        tempconst = matter.add.constraint(prevoius, current);
        
        previous = corrent;
    }
}
```

## Development

### Outcome

I started with leaning how to use python3 to host a local webserver:

```python
import http.server
import socketserver

PORT = 8080
Handler = http.server.SimpleHTTPRequestHandler

with socketserver.TCPServer(("", PORT), Handler) as httpd:
    print("serving at port", PORT)
    httpd.serve_forever()
```

This allows me to quickly develop on both the website and phaser project as it updates with a reload.

I then used a website template from W3Schools to quickly give it a nice look with a link to the phaser game on another page.

The phaser library imported easily and the demo scene worked and allowed me to test new ideas and different parts of the physics engine that would be useful for the simulation.

### Challenges

Getting the constraints to delete posed a challenge initially, but after I found the correct method in the matter documentation I was able to implement it.&#x20;

## Testing

### Tests

| Test | Instructions             | What I expect                                 | What actually happens | Pass/Fail |
| ---- | ------------------------ | --------------------------------------------- | --------------------- | --------- |
| 1    | Run local host           | Webpage is viewable on my local host          | As Expected           | Pass      |
| 2    | Run Phaser example scene | The webpage displays and run the phaser scene | As Expected           | Pass      |
| 3    | Run modified example     | The webpage displays and run the phaser scene | As Expected           | Pass      |

### Evidence

![Game Webpage](<../.gitbook/assets/image (1).png>)

![Phaser example scene](<../.gitbook/assets/image (7) (1).png>)

![Initial constraint between two objects](<../.gitbook/assets/image (3) (1).png>)

![Only the most recent two objects have a constraint](<../.gitbook/assets/image (5).png>)
