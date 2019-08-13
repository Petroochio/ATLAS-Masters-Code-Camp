# ATLAS Code Camp: Day 2

## Examples!
Processing has loads of examples to introduce you to different concepts and capabilities. You can use these to get inspiration or figure out how to solve a problem. Check them out under File->Examples in the Processing editor. Also don't forget to check out the Processing [reference](https://processing.org/reference/) if you are curious about what Processing can do.

## Repeating Ourselves
As you saw with the functions yesterday, it is very easy to do the same thing over and over again in code. With the code for drawing multiple trees, we turned it into a function, and then called that function multiple times. But if we want to create more than a couple of trees, we would again end up with a bunch of lines of code that look the same. We can avoid that with loops!

## Loops
Our first topic today will be loops, which is a block of code that will repeatedly run until we tell it to stop or the controller runs out of memory. Here is some starter code for a while loop that will draw some ellipses randomly on the screen.
```
void setup() {
  size(640, 480);
  background(180, 200, 255);
  noStroke();

  while (true) {
    ellipse(random(width), random(height), 50, 50);
  }
}
```
*Note: the width and height variables are instances of global variables in Processing. Will discuss these more later*

This should draw a bunch of white circles on a friendly blue background, it kind of looks like clouds. `while` is the keyword for defining a loop, the parenthesis are the condition for whether the loop should continue running. In this case we are passing a boolean value `true` into the condition so it will run forever. The code between the `{ }` is what the loop will repeatedly execute. One of the issues here though is it that since this loop has no end point, the program is running out of memory and only generates a certain number of circles.

### Ending Loops
If we wanted to run a loop that generated 50 circles, it would look like this.
```
void setup() {
  size(640, 480);
  background(180, 200, 255);
  noStroke();

  int count = 0;
  while (count < 50) {
    ellipse(random(width), random(height), 50, 50);
    count = count + 1;
  }
}
```
We've changed two things in our loop. First we have added an `int` variable that we can use store how man times the loop has run, and inside the loop we are adding 1 to the count every iteration. We've also changed are condition from `true` to `count < 50`. If we were to convert this block of code to english, it would roughly say "while count is less than fifty, draw an ellipse and increment the count by one." When count is less than 50, our condition yields true, so the loop will continue, and otherwise it will yield false, and then the loop will stop. Loops must run to completion before any following code will be reached.

### The Final Tree Activity
Take your tree sketch from the last class and modify it to use a while loop to randomly place some trees.

### Global Variables
Before we move on, I wanted to quickly touch on some important global variables in Processing you might want to use. Global Variables are ones that are accessible throughout the application and are set by Processing itself.
- `width`: Width of the window
- `height`: Height of the window
- `mouseX`: Current X component of the mouse position
- `mouseY`: Current X component of the mouse position
- `mousePressed`: boolean value that stores whether or not the mouse is currently pressed

### The Draw Loop
While loops are great, but they only run once and global varibles like `mouseX` won't change inside of a while loop. Thankfully Processing has a feature for us that will allow us to run code once every update. Similar to `setup` which we have defined previously, `draw` is a function we can define that runs repeatedly until the program is closed. Each run of the draw loop is often called a frame.
```
void setup() {
  size(640, 480);
  background(180, 200, 255);
}

void draw() {
  noStroke();
  ellipse(random(width), random(height), 50, 50);
}
```
If we want to clear the screen at the start of every frame we can simply call the `background(r, g, b)` function before we draw our shapes.

### Mouse Draw
Now take what we learned about the draw loop, and let's apply it to some of our global variables. Make a sketch that draws whichever shape you prefer at the current mouse position each frame.

### Conditional Statements
We previously saw a conditional in our while loop, in which the condition told it when to stop. There will be many situations when you are coding when you want to execute some code under specific conditions. For that we have a bunch of contitional statements to help us. The most basic is `if`. Put the following in your draw.
```
if (mouseX > width / 2) {
  background(0);
}
```
When this runs you should see the color of the sketch change to black when the mouse is halfway across the window.

Following if, we can have else, which allows us to execute code in the case that our condition is not met.
```
if (mouseX > width / 2) {
  background(0);
} else {
  background(200);
}
```
Now the background should be gray when the mouse is on the left side of the screen, and black when the mouse is on the right.

Finally we have else if, which enables us to chain multiple if statements together where only the first one in the chain that passes it's condition will execute.
```
if (mouseX < width / 4) {
  background(0);
} else if (mouseX < width / 2) {
  background(80);
} else if (mouseX < width / 4 * 3) {
  background(160);
} else {
  background(240);
}
```

### Click and Drag to Draw
Now using and if statment to determine if the mouse is pressed, modify your previous mouse draw code to only draw when the user clicks and drags.

### Comparative Operators
Just like for math operations, we have operators for making comparisons between values. Some operators consist of multiple symbols. These comarisons always return a boolean value of either `true` or `false`.

- `==`: Returns true if the values on both sides are equal `1 == 1`
- `!=`: Returns true if the values on both sides are not equal `1 != 2`
- `>`: Returns true if the value on the left side is greater than the value on the right `2 > 1`
- `>=`: Returns true if the value on the left side is greater than or equal the value on the right `1 >= 1`
- `<`: Returns true if the value on the left side is less than the value on the right `1 < 2`
- `<=`: Returns true if the value on the left side is less than or equal to the value on the right `1 <= 1`

### On To The DVD
Using what we just learned about conditionals gives us everything we need to create a knock off of those old DVD screen saver, first let's get the setup out of the way.
```
int logoSize = 30;
int x;

void setup() {
  size(1280, 720);
  x = width / 2;
}

void draw() {
  // this is where our update code will go

  // draw commands
  noStroke();
  background(10);
  rect(x - logoSize / 2, height / 2, logoSize, logoSize);
}
```
So our basic setup has a variable for the x position of the logo, and a variable for the logo size. In our set up we set the initial x position, and to show it we are drawing the rect with at `x - logoSize / 2` so that it is centered on our actual x position. To get it to move, let's add another variable for the speed it will move at and add it to the x coordinate every frame.
```
int logoSize = 30;
int x;
int speedX = 7; // 7 is arbitary, try different speeds to see how it feels.

void setup() {
  size(1280, 720);
  x = width / 2;
}

void draw() {
  // update
  x += speedX;

  // draw commands
  noStroke();
  background(10);
  rect(x - logoSize / 2, height / 2, logoSize, logoSize);
}
```
You'll notice that our square is disappearing off of the side of the screen, oh no! Let's fix this, we will use a simple if statement to change the speed when it passes the wall on the right.
```
int logoSize = 30;
int x;
int speedX = 7; // 7 is arbitary, try different speeds to see how it feels.

void setup() {
  size(1280, 720);
  x = width / 2;
}

void draw() {
  // update
  // we are subtracting half the size from the width so it bounces exactly
  // at the edge instead of a little after
  if (x > width - logoSize / 2) {
    speedX = speedX * -1;
  }
  x += speedX;

  // draw commands
  noStroke();
  background(10);
  rect(x - logoSize / 2, height / 2, logoSize, logoSize);
}
```
Now it's disappearing of the left side. To fix this we can add an *or* clause to our bounce condition, this is done by putting `||` between the two expressions. Replace your if statment with
```
if (x > width - logoSize / 2 || x < logoSize / 2) {
  speedX = speedX * -1;
}
```
Great! Our square is trapped on the screen forever. On your own add a y coordinate that changes over time like our x, and make it bounce off of the top and bottom of the window.

Finally, if you want to swap in an image for the logo, look at the [Processing reference](https://processing.org/reference/image_.html) for how to use images
