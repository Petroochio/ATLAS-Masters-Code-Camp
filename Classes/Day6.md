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
int spaceBtw = 30; // space between boxes

void setup() {
  size(640, 480);
  ballPos = new PVector(width / 2, height * 3 / 4);
  paddleY = height - 50;
}
```


