# 2.2.1 Cycle 1

## Design

### Objectives

In this cycle, I aim to have the basic framework for the webpage and phaser library ready to build on top of it. I will aim to implement the base phaser demo within a webpage. I would like to get this done before any game / simulation design so I know it would work on a browser.

* [x] Get a local web host working
* [x] Set up a basic webpage
* [x] Implement Phaser.io into the webpage
* [x] Get the Phaser.io demo working

### Usability Features

* Working href links
* Good contrast on the website for readability

### Key Variables

| Variable Name | Use |
| ------------- | --- |
|               |     |

### Pseudocode

```
<a href="game_page.htm">Game Page</a>
```

## Development

### Outcome

I started with leaning how to use python3 to host a local webserver:

```
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

The phaser library imported easily and the demo scene worked and even with some quick modifications (e.g making the canvas larger to see if the physics still work), it still worked as expected.

### Challenges

Trying to get the demo background in phaser to iteratively extend to the left when I resized the window proved a little difficult when getting to grip with the library, but I got it working n the end.

## Testing

### Tests

| Test | Instructions                 | What I expect | What actually happens | Pass/Fail |
| ---- | ---------------------------- | ------------- | --------------------- | --------- |
| 1    | Run code                     | Demo works    | Demo works            | Pass      |
| 2    | Run code with extended scene | Demo works    | Demo works            | Pass      |

### Evidence

