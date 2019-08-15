# ATLAS Code Camp: Day 6

## Recap of Loading In a Text File
Yesterday we loaded in a text file (specifically a .json file) to our lights out game as a config. Here is the code we ended up with.
```
int gridSize;
boolean[][] cells;
boolean lastFramePressed = false;

void setup() {
  size(720, 720);
  background(0);
  
  JSONArray grid = loadJSONArray("level1.json");

  gridSize = grid.size();
  cells = new boolean[gridSize][gridSize];

  for (int row = 0; row < grid.size(); row += 1) {
    JSONArray rowArray = grid.getJSONArray(row);

    for (int col = 0; col < rowArray.size(); col += 1) {
      cells[row][col] = rowArray.getBoolean(col);
    }
  }
}

void draw() {
  int cellSize = width / gridSize;

  if (mousePressed && !lastFramePressed) {
    // Loop through all of the cells
    for (int row = 0; row < cells.length; row += 1) {
      int cellY = row * cellSize;

      for (int col = 0; col < cells[row].length; col += 1) {
        int cellX = col * cellSize;

        if (mouseX > cellX && mouseX < cellX + cellSize) {
          if (mouseY > cellY && mouseY < cellY + cellSize) {
            cells[row][col] = !cells[row][col];
            
            if (row != 0) {
              cells[row - 1][col] = !cells[row - 1][col];
            }
          }
        }
      }
    }
  }
  lastFramePressed = mousePressed;
  
  // draw code
  background(0);
  for (int row = 0; row < gridSize; row += 1) {
    int cellY = row * cellSize;
    for (int col = 0; col < gridSize; col += 1) {
      int cellX = col * cellSize;
      if (cells[row][col]) fill(250);
      else fill(0);

      rect(cellX, cellY, cellSize, cellSize);
    }
  }
}
```
And this is what your .json file should look like
```
[
  [ false, true, false, false, false ],
  [ false, true, false, false, true ],
  [ false, true, false, false, true ],
  [ false, false, false, true, false ],
  [ false, false, false, true, false ]
]
```

If you notice, when we load the file we get something that isn't like a normal array. It has a type of `JSONArray`, which doesn't fit within the set of types we spoke of before. That's because `JSONArray` is a custom type created created for processing that we call a **class**. There are many other classes in the processing environment as well, and we can define our own that I will show you later. We call each instance of a class, like `grid`, an **object** For now go look at the [Processing Reference for JSONArray](https://processing.org/reference/JSONArray.html) and look at what other properties it has.

### Methods
You may notice that in our code from before we do `grid.size()` to get the size of our loaded array. It looks really similar to a function, except it's attached to our grid object with a `.` This is called a **method**, it works specifically on the context of the object we are calling it from. We will be using a lot of methods today.

## Using Objects in Breakout
Let's make Breakout again, but this time use objects. Here is the code we ended up with before adding all of the extra stuff.
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
int boxH = 20;
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

### PVector
Processing has useful class built in called [PVector](https://processing.org/reference/PVector.html). We can use PVectors to store 2D data (x, y). PVectors for our uses are going to represent 2D directions. Let's start with changing the ball position in our game to be a PVector. Replace our `int x;` and `int y;` code with `PVector ballPos;` (ballPos is short for *ball position*). Then in setup replace the spot where we assign x and y, with `ballPos = new PVector(width / 2, height * 3 / 4);`. Notice when creating an instance of the PVector class we need to use the keyword `new`, you will always need to do this when using classes. Let's do the same for our speed. Replace the speedX and speedY with a single PVector like this `PVector ballSpeed = new PVector(3, -3);`

Here is what our new setup section should look like
```
PVector ballPos;
int ballSize = 20;
PVector ballSpeed = new PVector(3, -3);
int paddleW = 200;
int paddleH = 20;
int paddleY;

boolean[] row0 = { true, true, true, true, true };
int boxW = 70;
int boxH = 20;
int spaceBtw = 30; // space between boxes

void setup() {
  size(640, 480);
  ballPos = new PVector(width / 2, height * 3 / 4);
  paddleY = height - 50;
}
```
Now we need to go update all of our code in the draw loop to work with PVectors.

### Properties of PVectors
To get the data out of a PVector we have to access it's `x` and `y` properties. If we wanted the x position of the ball we would do this with `ballPos.x`. Similar to how methods are called, properties are components of an object that are unique to each instance. Go through the draw code and replace the x, y, speedX, and speedY with the correct PVector property. Your draw code should look like this. We will also need to change our `checkBoxCollision` function.

```
boolean checkBoxCollision(int boxX, int boxY, int boxW, int boxH) {
  if(ballPos.x > boxX && ballPos.x < boxX + boxW) {
    if (ballPos.y > boxY && ballPos.y < boxY + boxH) {
      return true;
    }
  }
  
  return false;
}

void draw() {
  // Update code
  // wall collision
  if (ballPos.x > width - ballSize / 2 || ballPos.x < ballSize / 2) {
    ballSpeed.x = ballSpeed.x * -1;
  }
  if (ballPos.y > height - ballSize / 2 || ballPos.y < ballSize / 2) {
    ballSpeed.y = ballSpeed.y * -1;
  }

  // box collision
  for (int i = 0; i < row0.length; i += 1) {
    if (row0[i]) {
      int leftPadding = ((width + spaceBtw) - row0.length * (boxW + spaceBtw)) / 2;
      int boxX = i * (boxW + spaceBtw) + leftPadding;
      int boxY = 20;
      
      if (checkBoxCollision(boxX, boxY, boxW, 20)) {
        ballSpeed.y = -ballSpeed.y;
        row0[i] = false;
      }
    }
  }
  
  // paddle collision
  if (checkBoxCollision(mouseX - paddleW / 2, paddleY - paddleH / 2, paddleW, paddleH)) {
    ballSpeed.y = -ballSpeed.y;
  }

  // Move ball
  ballPos.x += ballSpeed.x;
  ballPos.y += ballSpeed.y;

  // Draw commands
  background(10); // Clears the screen every frame

  noStroke();
  fill(250);
  // draw ball
  ellipse(ballPos.x, ballPos.y, ballSize, ballSize);
  
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
Great, now we have fewer things to keep track of as we play! Now let's see if we can do the same thing for all of the bricks.

## Array Lists
The JSONArray object from before is actually based on another object that is common throughout many programming languages, the [**ArrayList**](https://processing.org/reference/ArrayList.html). ArrayLists, sometimes referred to as Lists, are essentially arrays that change in size. This means we can add and remove objects from our projects at run time, instead of having to create those large 2D arrays from before. ArrayLists can be used to store any type of data, let's replace our bricks array of booleans with an ArrayList of PVectors. To do that remove the spot where we declare `row0` and replace it with the following
```
ArrayList<PVector> bricks = new ArrayList<PVector>();
```
When we create a new ArrayList though, it has nothing in it, we have to add elements to it using the `.add` method. In our setup let's add the code to generate the row from before.
```
int numColumns = 5;
for (int i = 0; i < numColumns; i += 1) {
  int leftPadding = ((width + spaceBtw) - numColumns * (boxW + spaceBtw)) / 2;
  int boxX = i * (boxW + spaceBtw) + leftPadding;
  int boxY = 20;
  bricks.add(new PVector(boxX, boxY));
}
```
Now we need to go through our code and change the spots that reference a box and change it. First lets do the collision code. Let's change the `checkBoxCollision` function again to accept only a PVector.
```
boolean checkBoxCollision(PVector boxPos, int w, int h) {
  if(ballPos.x > boxPos.x && ballPos.x < boxPos.x + w) {
    if (ballPos.y > boxPos.y && ballPos.y < boxPos.y + h) {
      return true;
    }
  }
  
  return false;
}
```
Now let's change the for loop that's actually checking all of the boxes. We are going to be removing a box when it hit's the ball now, to do this we will use the `.remove` method, which can take an index or an object to remove from the array. We also won't need to do any more calcuations for the positions since all of that data is stored. Also instead of using `[i]` to get an object we will need to use the get method `.get(i)`.
```
for (int i = 0; i < bricks.size(); i += 1) {  
  if (checkBoxCollision(bricks.get(i), boxW, boxH)) {
    ballSpeed.y = -ballSpeed.y;
    bricks.remove(i);
  }
}
```
Now let's do the same for the draw code.
```
for (int i = 0; i < bricks.size(); i += 1) {
  PVector b = bricks.get(i);
  rect(b.x, b.y, boxW, boxH);
}
```
Also don't forget to update the paddle collision to work with our new box collision function.
```
PVector paddlePos = new PVector(mouseX - paddleW / 2, paddleY - paddleH / 2);
if (checkBoxCollision(paddlePos, paddleW, paddleH)) {
  ballSpeed.y = -ballSpeed.y;
}
```
Great, now we've got all of the boxes in one list, and we only have to set their position once. See if you can add more boxes or rows to our bricks ArrayList!

## Making Our Own Class
Will be covered during class
