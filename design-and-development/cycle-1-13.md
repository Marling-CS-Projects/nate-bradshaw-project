# 2.2.14 Cycle 14 - Webpage improvements and hosting.

## Overview

For this cycle I will improve the website surrounding the project to add instructional material alongside a couple visual tweaks.

## Design

### Objectives&#x20;

* [x] Add instructions index webpage
* [x] Create an instructional image
* [x] Create a favicon for the website
* [x] Host the webpage on GitHub Pages

### Pseudocode

```html
<link rel="icon" href="WebpageIcon.png">

<p>*instructions*<p>
<img src="InstructionalImage.png">
```

## Development

### Outcome

To make the instructions for the webpage, I decided that a visually labelling a screenshot of the creature creator controls would be the best way to show the user how to use them. To do this, I used photoshop and added small descriptions to each button, dropdown and slider.&#x20;

<figure><img src="../.gitbook/assets/Guide (1).png" alt=""><figcaption><p>Guide.png</p></figcaption></figure>

For the text, I further explained the buttons and gave some advice to the user on how to create a more successful creature.

The webpage template I am already using has a space for text, so all I had to do is add the text and the image link into it.

```html
<div class="w3-row-padding w3-padding-64 w3-container">
    <div class="w3-content">
        <div class="w3-twothird">
            <h1>Instructions</h1>
            <h5 class="w3-padding-32">
                To simulate evolution of a creature, you first have to create one!
                To do this, use the controls under the window. The "joint" button
                creates circles which "muscles" can be attached to. The muscles
                are what moves the creature, so make sure to add plenty of them
                between the joints. When you're done, choose a simulation type by
                selecting a creature goal, set the time of each generation and press
                "done". You should be able to observe the creatures improving from as
                early as the 5th generation, with improvement continuing even further
                from there!
            </h5>

            <p class="w3-text-grey">>
                The goal of this project is to intuitively teach the fundamentals
                of real life evolutions through the machine learning based
                neuroevolution of AI creatures.
            </p>
        </div>

        <div class="w3-third w3-center">
            <img src="Guide.png">
        </div>
    </div>
</div>
```

For a final touch to the webpage, I decided to add a favicon. To do this I once again used photoshop and created a representation of a creature within a circle and added the link to the html.

<figure><img src="../.gitbook/assets/Icon (2).png" alt=""><figcaption><p>Icon.png</p></figcaption></figure>

```html
<!-- inside <head> on in both html files -->
<link rel="icon" href="Icon.png">
```

Finally, I went to the repository settings, and on the sub menu of "Pages" selected the main branch to be hosted.

[https://marling-cs-projects.github.io/Nate-Bradshaw-A-Level-Project/index.html](https://marling-cs-projects.github.io/Nate-Bradshaw-A-Level-Project/index.html)

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

<figure><img src="../.gitbook/assets/image (4) (4).png" alt=""><figcaption><p>game_page.html</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (2) (2).png" alt=""><figcaption><p>Favicon</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (3).png" alt=""><figcaption><p>Website hosted on GitHub Pages</p></figcaption></figure>
