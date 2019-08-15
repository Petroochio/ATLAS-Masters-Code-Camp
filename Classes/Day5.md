# ATLAS Code Camp: Day 5

## 2 Dimensional Arrays
Sometimes one array just isn't enough. In both our Bitmap creator and Breakout examples things began to get messy once we needed to have separate rows of objects. We can make things a little bit easier on ourselves with something called two dimensional arrays, or *2D Arrays*. Simply, 2D arrays are arrays with arrays inside of them, we could define one like this
```
int[][] lotsOfNumbers = {
  { 1, 2, 3 },
  { 4, 5, 6 }
}
```
2D arrays can still only store one data type unfortunately. You can also have 3D or 4D arrays, which follow similar rules. To get values out of a 2D array you will need to access it twice, here are some examples
```
lotsOfNumbers[0];
```
Will give us the first array, which looks like { 1, 2, 3 }
```
lotsOfNumbers[1][2];
```
Will give us the 3rd element of the second array (in this case it's 6), *remember arrays start at 0!*

Just like regular arrays, 2D arrays can store any type of data you want. This means we can make our code for our BitMap painter a lot simpler!

### Making BitMaps Better
Instead of creating a separate array for each set of pixels and writing all of those extra for loops, lets use a 2D array, here is where I left off on day 3. Remember the length property will give us the size of the array, this is going to become very useful soon.
```
int cellSize;
boolean[] cells = { false, false, false, false };
boolean lastFramePressed = false;

void setup() {
  size(720, 720);
  cellSize = width / cells.length;

  background(0);
}

void draw() {
  if (mousePressed && !lastFramePressed) {
    // Loop through all of the cells
    for (int i = 0; i < cells.length; i += 1) {
      int cellX = i * cellSize;
      int cellY = 0;

      if (mouseX > cellX && mouseX < cellX + cellSize) {
        if (mouseY > cellY && mouseY < cellY + cellSize) {
          cells[i] = !cells[i];

          if (cells[i]) fill(250);
          else fill(0);

          rect(cellX, cellY, cellSize, cellSize);
        }
      }
    }
  }
  lastFramePressed = mousePressed;
}
```
Ok, let's make our cells array 2D and give it a second row.
```
boolean[][] cells = {
  { false, false, false, false },
  { false, false, false, false }
};
```
Cell size isn't going to work anymore since our cells array is technically length 2 (2 rows), so let's remove that from setup and replace it with cellHeight and cellWidth later.

Now let's change our for loop. To iterate through a 2D array we are going to need nested for loops, we are going to name our iterators col and row, for the column and rows we are working through. Our cellY is going to increase based on the row number and our cellX will be based on the column.
```
int cellHeight = height / cells.length; // Divide the height by number of rows
for (int row = 0; row < cells.length; row += 1) {
  int cellWidth = width / cells[row].length;
  int cellY = row * cellHeight;

  for (int col = 0; col < cells[row].length; col += 1) {
    int cellX = col * cellWidth;

    if (mouseX > cellX && mouseX < cellX + cellWidth) {
      if (mouseY > cellY && mouseY < cellY + cellHeight) {
        // the current cell is cells[row][col]
        cells[row][col] = !cells[row][col];

        if (cells[row][col]) fill(250);
        else fill(0);

        rect(cellX, cellY, cellWidth, cellHeight);
      }
    }
  }
}
```
Now you should be able to click around and toggle cells on and off. Try adding and removing elements from our cells array to see what else is going on. And for reference here is the code you should have before experimentation.
```
int cellSize;
boolean[][] cells = {
  { false, false, false, false },
  { false, false, false, false }
};
boolean lastFramePressed = false;

void setup() {
  size(720, 720);

  background(0);
}

void draw() {
  if (mousePressed && !lastFramePressed) {

    int cellHeight = height / cells.length; // Divide the height by number of rows

    // Loop through all of the rows
    for (int row = 0; row < cells.length; row += 1) {
      int cellWidth = width / cells[row].length;
      int cellY = row * cellHeight;

      // Loop through all of the cells in the current row
      for (int col = 0; col < cells[row].length; col += 1) {
        int cellX = col * cellWidth;

        // Check if the mouse is in the bounds of our cell
        if (mouseX > cellX && mouseX < cellX + cellWidth) {
          if (mouseY > cellY && mouseY < cellY + cellHeight) {
            // the current cell is cells[row][col]
            cells[row][col] = !cells[row][col];

            if (cells[row][col]) fill(250);
            else fill(0);

            rect(cellX, cellY, cellWidth, cellHeight);
          }
        }
      }
    }
  }
  lastFramePressed = mousePressed;
}
```

### Another Game
There is a simple game called Lights Out! where when you click on one cell, all of the cells around it will toggle their state. We already have half of the structure to do this, so let's modify our code a little more.

First let's make our cells array a little more uniform. Let's make it a 4 by 4. We can do this without writing that long array literal. We are also going to use a gridSize so we only have to change one number if we want to change the grid
```
int gridSize = 4;
boolean[][] cells = new boolean[gridSize][gridSize]; // This creates a 4 by 4 array of false
```

Now let's quickly modify our drawing code so that it's actually drawing everything every frame instead of only when we click. To do this remove the draw code from our first for loop and place it below the mouse click statment in another nested for loop. Don't forget to draw the background every frame too! Your draw function should now look like this. We are going to create a single variable for cellSize since we know everything will be square
```
void draw() {
  int cellSize = width / gridSize; // Divide the height by number of rows
  
  if (mousePressed && !lastFramePressed) {
    for (int row = 0; row < cells.length; row += 1) {
      int cellY = row * cellSize;

      for (int col = 0; col < cells[row].length; col += 1) {
        int cellX = col * cellSize;

        if (mouseX > cellX && mouseX < cellX + cellSize) {
          if (mouseY > cellY && mouseY < cellY + cellSize) {
            // the current cell is cells[row][col]
            cells[row][col] = !cells[row][col];
          }
        }
      }
    }
  }
  lastFramePressed = mousePressed;
  
  // draw code
  background(0);
  for (int row = 0; row < cells.length; row += 1) {
    int cellY = row * cellSize;

    for (int col = 0; col < cells[row].length; col += 1) {
      int cellX = col * cellSize;
        if (cells[row][col]) fill(250);
        else fill(0);

        rect(cellX, cellY, cellSize, cellSize);
    }
  }
}
```

### Toggling Adjacent Cells
Since we have a complete working grid now, and our array is uniform it will be easy to tell what the adjacent cells are when we click. The cell above will be in `row - 1`, the cell to the right will be in `col + 1`. Let's just toggle the cell above first, right after the code for toggling a cell when you click let's add this
```
cells[row - 1][col] = !cells[row - 1][col];
```
You might notice we get an error when clicking on a cell in the top row, oh no! We can actually fix this easily, we just need to write an if statement that only let's us toggle the cell above if we are not clicking in the first row. The conditional operator for "does not equal" is `!=`. Replace the code we just added with this
```
if (row != 0) {
  cells[row - 1][col] = !cells[row - 1][col];
}
```
Yay, no more errors!

Now add the cell toggling for all of the other adjacent cells on your own! You can even switch back to using array literals if you want to mess around with the starting state.

## Loading data in from a file
We will walk through this section in class, and I will update this page based on how far we get.
