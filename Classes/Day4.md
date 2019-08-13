# ATLAS Code Camp: Day 4

## Recap with Breakout
Today we will do a recap by building the game breakout. This activity is based on the [excellent tutorial](https://developer.mozilla.org/en-US/docs/Games/Tutorials/2D_Breakout_game_pure_JavaScript) from Mozilla for JavaScript Game Development.

### Getting Started
Let's get our basic Processing setup going. Remember the `setup` and `draw` functions we are defining are automatically called by processing at run time.
```
void setup() {
  size(640, 480);
}

void draw() {
  // Update code
  // Place our update code here later

  // Draw commands
  background(10); // Clears the screen every frame

  // Put our later draw code here
}
```

### Getting a Moving Ball
Just like we did for the DVD activity let's create position and speed variables above the setup function.
```
int x;
int y;
int ballSize = 20;
int speedX = 3;
int speedY = -3;
```
And inside the `setup` let's initialize the x and y positions right below the `size` function.
```
x = width / 2;
y = height / 4 * 3; // 3/4 of the way down the screen
```
To make it move, in our update section of the `draw` function let's make the ball move.
```
x += speedX;
y += speedY;
```
And we need to draw the ball beneath the `background` function which is clearing the screen.
```
noStroke();
fill(250);
ellipse(x - ballSize / 2, y - ballSize / 2, ballSize, ballSize);
```
So far your code should look like this, and you should have a ball that moves off of the top left hand corner of the screen.
```
int x;
int y;
int ballSize = 20;
int speedX = 3;
int speedY = -3;

void setup() {
  size(640, 480);
  x = width / 2;
  y = height / 4 * 3; // 3/4 of the way down the screen
}

void draw() {
  // Update code
  x += speedX;
  y += speedY;

  // Draw commands
  background(10); // Clears the screen every frame

  noStroke();
  fill(250);
  ellipse(x - ballSize / 2, y - ballSize / 2, ballSize, ballSize);
}
```

### Make it Bounce off the Walls
We want our ball to stay on the screen for ever, to do this we can simply use the code we had from the DVD activity. Right above the code for updating the position of the ball, add two if statements for bouncing off the walls like this.
```
if (x > width - ballSize / 2) {
  speedX = speedX * -1;
}
```
This will make the ball only bounce off of the right wall, to have it work on the ceiling too we will need another if statment for the y position.
```
if (y < ballSize / 2) {
  speedY = speedY * -1;
}
```
We will still have an issue with the ball flying off the left side or the bottom of the screen, use and `||` (or condition) to handle the other walls.

All in all your new draw function should look like this
```
void draw() {
  // Update code
  if (x > width - ballSize / 2 || x < ballSize / 2) {
    speedX = speedX * -1;
  }
  if (y > height - ballSize / 2 || y < ballSize / 2) {
    speedY = speedY * -1;
  }
  x += speedX;
  y += speedY;

  // Draw commands
  background(10); // Clears the screen every frame

  noStroke();
  fill(250);
  ellipse(x, y, ballSize, ballSize);
}
```

### The Paddle
Now let's draw a paddle centered on the mouse x position at the bottom of the screen. I'm going to make mine 100px wide and 20px tall. In my draw section I'm going to add the following.
```
// put these above setup
int paddleW = 100;
int paddleH = 20;
int paddleY;

// Put this in setup
paddleY = height - 50;

// put this in draw
rect(mouseX - paddleW / 2, height - 50, paddleW, paddleH);
```
The ball doesn't hit the paddle yet, let's get that going. To do this we are going to need to create some simple rectangle collision code. In our update loop add the following.
```
if(x > mouseX - paddleW / 2 && x < mouseX + paddleW / 2) {
  if (y > paddleY - paddleH / 2 && y < paddleY + paddleH / 2) {
    speedY = -speedY;
  }
}
```
The first part of this if statement is checking if the ball is inside the x bounds of the box, and the second is checking if it's inside the Y bounds. The method we are using is called *axis aligned collision detection* which means that we are assuming our collision box will never be rotated. Your code should now look like this.
```
int x;
int y;
int ballSize = 20;
int speedX = 3;
int speedY = -3;
int paddleW = 100;
int paddleH = 20;
int paddleY;

void setup() {
  size(640, 480);
  x = width / 2;
  y = height / 4 * 3; // 3/4 of the way down the screen
  paddleY = height - 50;
}

void draw() {
  // Update code
  if (x > width - ballSize / 2 || x < ballSize / 2) {
    speedX = speedX * -1;
  }
  if (y > height - ballSize / 2 || y < ballSize / 2) {
    speedY = speedY * -1;
  }
  
  // paddle collision
  if(x > mouseX - paddleW / 2 && x < mouseX + paddleW / 2) {
    if (y > paddleY - paddleH / 2 && y < paddleY + paddleH / 2) {
      speedY = -speedY;
    }
  }

  // Move ball
  x += speedX;
  y += speedY;

  // Draw commands
  background(10); // Clears the screen every frame

  noStroke();
  fill(250);
  ellipse(x, y, ballSize, ballSize);
  
  int paddleW = 100;
  int paddleH = 20;
  rect(mouseX - paddleW / 2, height - 50, paddleW, paddleH);
}
```

### Adding the Boxes
Now let's add some boxes. For this version we are going to keep it simple and have a separate array of boxes for each row. We are going to add collision detection for each box by turning our code before into a function. First things first, let's start with one row, create an array above setup.
```
boolean[] row0 = { true, true, true, true, true };
int boxW = 70;
int spaceBtw = 30; // space between boxes
```
We are going to assume that each true represents a box that is present. Next let's loop through the boxes and draw them. I am going to make sure there is some padding on both sides of the screen. To get the padding we need to calculate the total amount of space that the boxes need, including the space between them amd subtract that from the width, then divide by 2 to center it. This is the code for drawing our boxes so far, we also have an if statment wrapping everything so that if any of the boxes are false we don't show them.
```
for (int i = 0; i < row0.length; i += 1) {
  if (row0[i]) {
    int leftPadding = ((width + spaceBtw) - row0.length * (boxW + spaceBtw)) / 2;
    int boxX = i * (boxW + spaceBtw) + leftPadding;
    int boxY = 20;
    rect(boxX, boxY, boxW, 20);
  }
}
```
Now to do collision detection, we can use our code from the paddle and make a function that returns true when the ball is colliding with something, then use it for all of the boxes. Define that function outside of the `draw` and `setup` functions.
```
boolean checkBoxCollision(int boxX, int boxY, int boxW, int boxH) {
  if(x > boxX && x < boxX + boxW) {
    if (y > boxY && y < boxY + boxH) {
      return true;
    }
  }
  
  return false;
}
```
Return statements will prevent all of the code after them from executing, so our final `return` statement here will only be reached if there is no collisions. This means we don't need an else in this specific case since we only want to `return true;` when it passes all of our conditions. Let's test out the function by replacing our paddle collision statement with it.
```
if (checkBoxCollision(mouseX - paddleW / 2, paddleY - paddleH / 2, paddleW, paddleH)) {
  speedY = -speedY;
}
```
We can do the same for each one of our boxes with a similar loop to the draw loop.
```
for (int i = 0; i < row0.length; i += 1) {
  if (row0[i]) {
    int leftPadding = ((width + spaceBtw) - row0.length * (boxW + spaceBtw)) / 2;
    int boxX = i * (boxW + spaceBtw) + leftPadding;
    int boxY = 20;
    
    if (checkBoxCollision(boxX, boxY, boxW, 20)) {
      speedY = -speedY;
      row0[i] = false;
    }
  }
}
```
Yay! Now we have all the basic ingredients for our Breakout clone. Add some flair of your own or continue with the additional goals I've provided. Here is what everything should look like before you tackle those additional goals.
```
int x;
int y;
int ballSize = 20;
int speedX = 3;
int speedY = -3;
int paddleW = 200;
int paddleH = 20;
int paddleY;

boolean[] row0 = { true, true, true, true, true };
int boxW = 70;
int spaceBtw = 30; // space between boxes

void setup() {
  size(640, 480);
  x = width / 2;
  y = height / 4 * 3; // 3/4 of the way down the screen
  paddleY = height - 50;
}

boolean checkBoxCollision(int boxX, int boxY, int boxW, int boxH) {
  if(x > boxX && x < boxX + boxW) {
    if (y > boxY && y < boxY + boxH) {
      return true;
    }
  }
  
  return false;
}

void draw() {
  // Update code
  // wall collision
  if (x > width - ballSize / 2 || x < ballSize / 2) {
    speedX = speedX * -1;
  }
  if (y > height - ballSize / 2 || y < ballSize / 2) {
    speedY = speedY * -1;
  }

  // box collision
  for (int i = 0; i < row0.length; i += 1) {
    if (row0[i]) {
      int leftPadding = ((width + spaceBtw) - row0.length * (boxW + spaceBtw)) / 2;
      int boxX = i * (boxW + spaceBtw) + leftPadding;
      int boxY = 20;
      
      if (checkBoxCollision(boxX, boxY, boxW, 20)) {
        speedY = -speedY;
        row0[i] = false;
      }
    }
  }
  
  // paddle collision
  if (checkBoxCollision(mouseX - paddleW / 2, paddleY - paddleH / 2, paddleW, paddleH)) {
    speedY = -speedY;
  }

  // Move ball
  x += speedX;
  y += speedY;

  // Draw commands
  background(10); // Clears the screen every frame

  noStroke();
  fill(250);
  // draw ball
  ellipse(x, y, ballSize, ballSize);
  
  // draw paddle
  int paddleW = 100;
  int paddleH = 20;
  rect(mouseX - paddleW / 2, height - 50, paddleW, paddleH);
  
  // Draw boxes
  for (int i = 0; i < row0.length; i += 1) {
    if (row0[i]) {
      int leftPadding = ((width + spaceBtw) - row0.length * (boxW + spaceBtw)) / 2;
      int boxX = i * (boxW + spaceBtw) + leftPadding;
      int boxY = 20;
      rect(boxX, boxY, boxW, 20);
    }
  }
}
```

### Some other goals for you
- Add more rows of boxes
- Make the ball speed up every time it hits something
- Make the ball reset when it hit's the bottom
- In the real breakout the angle of the ball is changed by it's distance from the center of the paddle, you can use the [abs](https://processing.org/reference/abs_.html) function to help.
- Make it pretty!
