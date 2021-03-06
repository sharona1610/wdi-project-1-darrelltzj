# Avoid the Raindrops
[**Avoid the Raindrops**](https://darrelltzj.github.io/wdi-project-1-darrelltzj/) is a game developed for a [project assignment](https://jeremiahalex.gitbooks.io/wdi-sg/content/11-projects/project-1/readme.html) at General Assembly's Web Development Immersive (WDI) Course.

Black the cat dislikes the rain. In the game, each player controls their version of Black the cat by maneuvering it along the screen's x-axis. Each cat has nine lives and loses one whenever it comes into contact with a rain droplet. As the game progresses, the frequency of the raindrops increases. The game has five stages with an additional bonus round. The aim is for each player to last the longest. The game ends when the player's cat loses all its lives.

*[Avoid the Raindrops](https://darrelltzj.github.io/wdi-project-1-darrelltzj/) requires a physical keyboard and is best played on Chrome Desktop Browser Version 56 or later.*

## How to play
The instructions below explain the game play and how the interface can be controlled.

### Key Control Summary
| Key           | Function      |
| ------------- |:-------------:|
| Escape        | Reload / Restart|
| Space         | Proceed / Pause |
| m             | Mute / Unmute   |
| Up Arrow      | Player 1 Select|
| Left Arrow    | Player 1 Left  |
| Down Arrow    | Player 1 Select|
| Right Arrow   | Player 1 Right |
| w             | Player 2 Select|
| a             | Player 2 Left  |
| s             | Player 2 Select|
| d             | Player 2 Right |

### Player Selection
The game starts off at the player control selection page and can be played with either one or between two players. Players may use the *Left* and *Right* arrows or the *a* and *d* keys to toggle their selections.

Players may either use the mouse or the keyboard to control their cats. They may do so by pressing the *Up* and *Down* arrows and the *w* and *s* keys at the player selection page. Only one player can select the mouse at each time.

Once players are comfortable with their control selections, they may press *Space* to begin the game.

### Controlling Black the Cat
When the game starts, the cat for player 1 starts on the right while the cat for player 2 starts on the left.

**Keyboard Controls:**

Player 1 uses the *Left* and *Right* arrows while player 2 uses the *a* and *d* keys to maneuver the cat left and right respectively.

**Mouse Control:**

If the mouse control was selected, shifting the cursor to either side of the cat would move it towards the respective directions. Placing the cursor within the cat's width would cause the cat to be stationary.

### Demo
<a href="http://www.youtube.com/watch?feature=player_embedded&v=p1edCVsrYaY
" target="_blank"><img src="http://img.youtube.com/vi/p1edCVsrYaY/0.jpg"
alt="IMAGE ALT TEXT HERE" width="480" height="360" border="10" /></a>

[Link to Game](https://darrelltzj.github.io/wdi-project-1-darrelltzj/)

## Application and Development Process
### Built With
[HTML Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) and [jQuery](https://jquery.com/) Application programming interface (API).

### Drawing on Canvas
[Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) draws images by creating image Objects and assigning their src, x and y positions, width and height. The *createFrame* function draws the image of the item (cat / raindrop) each time it is called. Each item is an Object that has the properties of their respective image folder, selected frame, image format, x and y positions and width and height.

```
function createFrame (item) {
  var image = new Image()
  image.src = 'assets/img/' + item.imageFolder + '/' + item.selectedFrame + item.imageFormat
  ctx.drawImage(image, item.posX, item.posY, item.width, item.height)
}
```

### Character Animation
The initial plan was to use this gif to simulate the cat running.

![Cat Gif](http://rs1366.pbsrc.com/albums/r779/mariperquinto/0550661001369318673_zpsc3a722ef.gif~c200)

However, as [Canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) was used in the making of the game, the canvas has to be cleared each time to remove and replace the previous positions of the characters and raindrops. This caused the cat gif to remain stationary like this:

![Stationary cat](http://i.imgur.com/IK7Vw7H.png)

As a workaround , the cat gif layers were separated.

![0](http://i.imgur.com/IK7Vw7H.png)
![1](http://i.imgur.com/IK7Vw7H.png)
![2](http://i.imgur.com/aDt2t02.png)
![3](http://i.imgur.com/OYO7J6R.png)
![4](http://i.imgur.com/RLy5z6y.png)
![5](http://i.imgur.com/uRbDIfe.png)
![6](http://i.imgur.com/NAs7ws1.png)
![7](http://i.imgur.com/0cIhzMR.png)

The code to control the cat's frame lies in the Character prototype *animateFrame*. Here is how it looks like:
```
Character.prototype.controlAnimation = function () {
  if (this.rightPressed || this.leftPressed || this.selectedFrame !== 0) {
    this.animateFrame()
  }
}
Character.prototype.animateFrame = function () {
  if (this.frameChangeDelay === 0) {
    if (this.selectedFrame === this.frameLength - 1) {
      this.selectedFrame = 0
    }
    else {
      this.selectedFrame++
    }
    this.frameChangeDelay = Math.floor(24 / this.frameLength)
  }
  else {
    this.frameChangeDelay--
  }
}
```
What this function does is that it selects each cat's image which would later be checked in the *createFrame* function. Each time this function is called, it changes the image. When it reaches the last image, it returns to the first image. A delay was later added to prevent the image from changing too fast.

**Delay:**

The delay was experimented around and is estimated to be around 0.05 seconds (20 images per second) for this set of 8 images.

It relies on the Canvas [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) function which changes the canvas at an estimated 1/60 seconds (60 times per second) each time.

*frameChangeDelay* was set at 24 / frame length. This means that it delays 3 times of [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame). The total delay is thus 3 * 1 / 60.

*This formula assumes that less delay is needed if there are more images, but must be experimented further if other images are used.*

**Flipping the image:**

The images could be scaled to -1 along the x-axis to flip the images, but as explained [here](http://stackoverflow.com/questions/8168217/html-canvas-how-to-draw-a-flipped-mirrored-image), the canvas context has to be saved, scaled and then restored, thus slowing down the game performance. To work around this, a separate file with the reverse images was created. The *faceOrientation* function controls which file (direction) is used. 0 was set as right facing images and 1 was set as left facing images. The *move* function changes the faceRight property each time the left or right arrow is pressed.

```
Character.prototype.faceOrientation = function () {
  if (this.faceRight) {
    this.orientation = 0
  }
  else if (!this.faceRight) {
    this.orientation = 1
  }
  this.imageFolder = this.mainImageFolder + '/' + this.orientation
}
```

### Raindrop Collision Detection
Each raindrop has a collided property that changes to true whenever the raindrop falls onto the ground or character.

**Raindrop falling on the ground:**

Raindrops collide with the ground whenever their height exceeds the ground (note that y increases downwards and x towards the right). Their y positions are changed back above the ground to ensure that they do not accelerate pass the ground.

![Collision with ground](http://i.imgur.com/SyRMvcg.jpg)
```
if (this.posY + this.height >= offsetPercent * canvasTag.height) {
  this.collided = true
  this.posY = offsetPercent * canvasTag.height - this.height
}
```

**Raindrop falling on a character:**

A similar method is used to detect any collision onto a character. This time, the raindrop must be within the width of the character as well. If collided, the character loses a life and the hit-indicator is activated.

![Collision Detection](http://i.imgur.com/80sDPbU.jpg)
```
else {
  characterArr.forEach(function (character) {
    if (!this.collided && this.posX > character.posX && this.posX + this.width < character.posX + character.width && (this.posY + this.height) >= character.posY) {
      this.collided = true
      this.posY = character.posY - this.height
      character.lives--
      indicatorX = this.posX
      indicatorY = this.posY - (this.height)
      activateIndicator = true
      $('#meow')[0].play()
    }
  }.bind(this))
}
```

### Spawning and Removing Raindrops

A raindrop is created and pushed to an array each time the timer counts down to 0. There is a separate *controlStage* function that changes the spawn duration as the game progresses.

The *checkRaindrops* function removes any raindrop that has collided. A delay is set to ensure that the splash image can be shown.

```
var raindropSpawnDuration = 20
var raindropRemovalDuration = 5
var raindropsArr = []
var raindropSpawnTimer = raindropSpawnDuration

function spawnRaindrops () {
  if (raindropsArr.length === 0 || raindropSpawnTimer <= 0) {
    var raindrop = new Raindrops()
    raindropsArr.push(raindrop)
    raindropSpawnTimer = raindropSpawnDuration
  }
  raindropSpawnTimer--
}
function checkRaindrops () {
  raindropsArr.forEach(function (raindrop, i) {
    createFrame(raindrop)
    raindrop.collisionDetection()
    raindrop.selectFrame()
    raindrop.move()
    if (raindrop.collided) {
      if (raindrop.raindropRemovalTime === 0) {
        raindropsArr.splice(i, 1)
      }
      else {
        raindrop.raindropRemovalTime--
      }
    }
  })
}
```

## Randomizing Raindrop Spawn Location
*randomX* function randomizes the raindrop spawn location.
```
Raindrops.prototype.randomX = function () {
  return Math.round(Math.random() * canvasTag.width)
}
```
## Raindrop Animation

**Raindrop movement:**

To animate the raindrop to appear like it is falling realistically, a gravity variable was set. This variable is constant but it changes the velocity which in turn, changes the y position of the raindrop. The [image](http://www.millersville.edu/physics/experiments/013/fig14p1.gif) below illustrates the difference on how the animation would differ if constant velocity is used compared to constant acceleration.

![vConstantVsAConstant](http://www.millersville.edu/physics/experiments/013/fig14p1.gif)
```
Raindrops.prototype.move = function () {
  if (!this.collided) {
    this.posY += this.velocity
    this.velocity += this.gravity
  }
  else {
    this.velocity = 0
    this.gravity = 0
  }
}
```

**Raindrop Splash**

To simulate the splash, the image is changed whenever the raindrop collides.

```
Raindrops.prototype.selectFrame = function () {
  if (this.collided) {
    this.selectedFrame = 1
  }
  else {
    this.selectedFrame = 0
  }
}
```

### Other Features

On blur listener:

As read [here](http://stackoverflow.com/questions/15871942/how-do-browsers-pause-change-javascript-when-tab-or-window-is-not-active), the requestAnimationFrame pauses while the setInterval timer continues running whenever the user switches the tab. This affected the game as the rain spawn duration is dependent on the game duration. The on blur listener below pauses the game whenever the tab becomes inactive to prevent this.

```
$(window).on('blur', activatePause)
```

## Areas to improve on

**Collision Detection on cat's body**

Currently, the game detects a collision when the raindrop hits the highest point of the cat (in this case, its head). This causes a collision to be detected when a raindrop passes the cat's head but before it hits the cat's body. A possible solution could be to split the cat's image by its head and body and detect collisions on each heights separately.

## Author(s)

- [Darrell Teo](https://github.com/darrelltzj)

Feel free to comment or contribute to this repository.

## References
* Canvas API, Collision detection and the left and right movement of the cat were referenced from https://developer.mozilla.org/en-US/docs/Games/Tutorials/2D_Breakout_game_pure_JavaScript

* Flipping images on Canvas http://stackoverflow.com/questions/8168217/html-canvas-how-to-draw-a-flipped-mirrored-image

* *function twoDigit()* for the timer on script.js was referenced from http://stackoverflow.com/questions/5774042/format-a-number-exactly-two-in-length

## Credits
This project is purely educational and experimental. It would not have been made possible without the following sources.

Images
- White Brick Wall by Josh Hahn from http://6iee.com/332876.html
- Running Cat from http://media.photobucket.com/
- Raindrop outline shape by Esteban Oliva from http://freevector.co/vector-icons/weather/raindrop-outlined-shape.html
- Splat by Clker-Free-Vector-Images from https://pixabay.com/en/splat-black-splatter-splash-ink-311428/
- Squash player by Creative Stall from the Noun Project
- Tennis Player Vector Icon by ProSymbols from the Noun Project
- Arrow by Numero Uno from the Noun Project
- Keyboard by Paul te Kortschot from the Noun Project
- WASD key from http://labs.minutelabs.io/Relativistic-Space-Sheep/
- Mouse by Anton Håkanson from the Noun Project
- mute volume by Ján Slobodník from the Noun Project

Audio
- River Flows in You by Yiruma from https://www.youtube.com/watch?v=rrDoJ8Dk-oQ
- Rain Sound by Relax With Nature from https://www.youtube.com/watch?v=GlO3MdKTpd4
- Blop by Mark DiAngelo from http://soundbible.com/2067-Blop.html
- Cat sound effect - meow by Winry Marini from https://www.youtube.com/watch?v=HIdLXBn-Py0
