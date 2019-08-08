# ATLAS Code Camp: Day 2

## The Other Kind of Loop
Yesterday we talked about while loops and how to create them with a counter like this
```
void setup() {
  size(1280, 720);
  background(180, 200, 255);
  noStroke();

  int count = 0;
  while (count < 50) {
    ellipse(random(width), random(height), 50, 50);
    count = count + 1;
  }
}
```

### For Loops
There is another kind of loop specifically designed to iterations a certain number of times with more concise code. They are called `for` loops, and if we were to replace our code from yesterday with one it would look like this
```
void setup() {
  size(1280, 720);
  background(180, 200, 255);
  noStroke();

  for (int i = 0; i < 50; i += 1) {
    ellipse(random(width), random(height), 50, 50);
  }
}
```
With a for loop we can define the counting variable, the condition for ending, and the amount to iterated by each step of the loop all in one line! Let's break it down really quick: `int i = 0;` is the variable declaration that happens at the start of the loop, `i < 50;` is the condition for ending the loop, and `i += 1` is what increments the loop counter which we call and *iterator*. The last part which is incrementing the iterator is executed once after every time code in the loop is run.

For loops are especially great for creating nice and neatly organized drawings. Let's say instead of randomly placing circles we would want to line them up nicely.
```
void setup() {
  size(1280, 720);
  background(0);
  noStroke();

  int circleSize = 50;
  for (int i = 0; i < 11; i += 1) {
    ellipse(i * (circleSize + 10), height / 2, circleSize, circleSize);
  }
}
```
To get them to line up here we are multiplying `i` by the size of the circles plus a little extra to give them some breathing room. Why don't we add some interaction to this.

### Activity: Mouse vs Loop
Create a drawing using a loop that responds to the movement of the mouse on the screen. Perhaps make the distance between the shapes smaller as the mouse moves closer to the center of the screen. Remember you will need to use the `void draw() {}` function to do this.

### Nested Loops
Sometimes you might what to create columns and rows in our drawings, we can do this by nesting a for loop inside of another one. In this clase we will increment our iterators by the size of the cirlces we are drawing.
```
void setup() {
  size(1280, 720);
  background(0);
  noStroke();

  int circleSize = 40;
  for (int y = 0; y <= height; y += circleSize) {
    for (int x = 0; x <= width; x += circleSize) {
      fill(250);
      ellipse(x, y, circleSize, circleSize);
    }
  }
}
```

### Activity: Circle Gradient
Modify this loop to create a color gradient across the screen based on the x and y value of each circle. Then make it so the circles change color based on their distance from `mouseX` and `mouseY`. After those two mess around with other effects you can apply to drawings using loops.

### Making that Game
At the start of class I showed a game where you could click a cell and toggle it's color between black and white. To accomplish this we will need to learn one more concept, arrays!

### The Hard Way
Let's say we wanted to make a grid of cells that you could change the color of by clicking on them. Here is some code to get you started.
```
boolean lastFramePressed = false;

int cellSize;
int cell1x, cell1y;
boolean cell1On = false;

void setup() {
  size(1280, 720);
  background(0);
  noStroke();
  
  cellSize = width / 4;
  cell1x = 0;
  cell1y = 0;
}

void draw() {
  if (mousePressed && !lastFramePressed) {
    if (mouseX > cell1x && mouseX < cell1x + cellSize) {
      if (mouseY > cell1y && mouseY < cell1y + cellSize) {
        cell1On = !cell1On;
      }
    }
  }
  
  if (cell1On) fill(250);
  else fill(0);
  rect(cell1x, cell1y, cellSize, cellSize);
  
  lastFramePressed = mousePressed;
}
```
We created 3 variables for cell1 with x and y positions and a state for on and off. Then we have a boolean for keeping track of if the mouse was pressed last frame, and if it wasn't and we are intersecting with a cell based on our if statements. Now you fill in the rest for the first row.

### Arrays Can Make Things Better
We can make our lives way simpler here with an array and some loops. An array is a collection of values of the same type stored under the same name. If we wanted to make an array of odd numers we could declare it like this
```
int[] odds = { 1, 3, 5, 7 };
print(odds[0]);
```
The first line is a declaration of an array, we need to add `[]` after `int` to tell processing we are making an array, and the second part `{ 1, 3, 5, 7 }` is a definition of the elements in the array. You can access each element by putting [integer] after the name. Array elements actually start at 0 instead of 1 so the first element in the array can be accessed with `odds[0]`. Change the number in the print statement above and look at the console. If you want to know how many items are in an array you can use the length property.
```
print(odds.length);
```
You can also define an empty array and put values in later. You must give it a set length though, arrays cannot change size over time.
```
int[] odds = new int[4];
odds[0] = 1;
odds[1] = 3;
odds[2] = 5;
odds[3] = 7;
```

Arrays play especially nicely with for loops, if we want to generate array values we can do it like this.
```
int[] doubles = new int[10];
for (int i = 0; i < 10; i += 1) {
  numbers[i] = i * 2;
}
```

### Making A Simpler Version
So applying what we learned about arrays, we can make a much more concise version of our code from before.
```
int cellSize;
int numCells = 4;
boolean[] cellsOn;
boolean lastFramePressed = false;

void setup() {
  size(720, 720);
  cellSize = width / numCells;
  cellsOn = new boolean[numCells];

  for (int i = 0; i < numCells; i += 1) {
    cellsOn[i] = false; 
  }
}

void draw() {
  background(0);

  if (mousePressed && !lastFramePressed) {
    for (int i = 0; i < numCells; i += 1) {
      int cellX = i * cellSize;
      int cellY = 0;

      if (mouseX > cellX && mouseX < cellX + cellSize) {
        if (mouseY > cellY && mouseY < cellY + cellSize) {
          cellsOn[i] = !cellsOn[i];
        }
      }
    }
  }
  lastFramePressed = mousePressed;

  for (int i = 0; i < numCells; i += 1) {
    if (cellsOn[i]) fill(250);
    else fill(0);

    rect(i * cellSize, 0, cellSize, cellSize);
  }
}
```

### Now Make One For All of the Rows!
Make a cell toggle for each one of the rows!

