# ATLAS Code Camp: Day 1

## What is Processing
Processing is a software "sketchbook" designed for learning code with and emphasis on visual arts. It's built using the Java programming language, which means any of the concepts we learn with Processing will apply to Java as well. There are a plethora of books and tutorials on Processing. For those with no Processing experience, I recommend *[Getting Started with Processing](https://www.amazon.com/gp/product/1457187086/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=1457187086&linkCode=as2&tag=processing09-20&linkId=JEJC22CTQ5ZF4ZYO)*. If you want to tackel some more advanced topics, *[The Nature of Code](https://natureofcode.com/)* is one of my favorite programming books ever, and it's free online!

## Getting Started
- Install Processing from [here](https://processing.org/download/)
- Then follow [this](https://processing.org/tutorials/gettingstarted/) tutorial to learn the basics. *In class we went through this together*

## Processing Basics
Because Processing is designed for artistic projects, each program in it is referred to as a sketch. A sketch can contain multiple files with the *.pde* extension.

### Coordinates
Throughout this course we will mostly be rendering in 2D, and coordinate system in Processing for 2D drawing uses X and Y. The Y axis is inverted in Processing 

### Drawing Shapes and Colors
Processing has loads of commands for you use to make your sketches look exactly the way you want. To draw anything to the screen, you must first call `stroke(r, g, b)` and/or `fill(r, g, b)`, then describe the shape.

### Some starter drawing commands
- `size(width, height)`: Sets the size of the processing window in pixels. [ref](https://processing.org/reference/size_.html)
- `background(red, green, blue)`: Fills the background of the processing window, using the provided color. [ref](https://processing.org/reference/background_.html)
- `ellipse(x, y, width, height);`: Draws an oval at given x and y positions with the given width and height. [ref](https://processing.org/reference/rect_.html)
- `rect(x, y, width, height);`: Draws an oval at given x and y positions with the given width and height. [ref](https://processing.org/reference/rect_.html)
- `triangle(x1, y1, x2, y2, x3, y3)`: Draws a triangle with the given points. [ref](https://processing.org/reference/triangle_.html)
- `fill(red, green, blue)`: Fills the currently queued drawing using the color provided. [ref](https://processing.org/reference/fill_.html)
- `stroke(red, green, blue)`: Strokes the currently queued drawing using the color provided. [ref](https://processing.org/reference/stroke_.html)
- `strokeWeight(weight)`: Sets the thickness of the stroke line. [ref](https://processing.org/reference/strokeWeight_.html)

### Order matters
Code is evaluated from left to right, then top to bottom. A good way to get familiar with this is to mess around the order of our draw code and see how it changes. Whatever you draw first will be at the back of your drawing, so it's important to consider the order of exectution when going for a specific effect.

### Make a Forest!
The activity for today's class is to make a forest using the draw code we learned before. Find a partner and together use the draw functions we learned to create a collection of trees.

### Keywords
This is the term for any phrase that a programming language recognizes by default, and corresponds to a specific functionality in the language, some keyword examples are int, function, if, and while. We will go over these keywords in the following lessons.

### Variables and Types
Variables are an essential part of programming. In simple terms they allow us to store data for future use and manipulation. You can use a variable many times troughout a program, and change it's value as the program runs. When you delcare a variable you must give it a type (for example `int x = 10;`). Here are some of the basic types we will use.

- `int`: Ints are an integer value, which means an int must be a whole number. ints in Processing can range from -2,147,483,647 to +2,147,483,647. (that's 32 bits!)
-  `floats`: Floats are the other important numerical type in Processing, they allow you to store decimal numbers. When you declare a float you should use a decimal point (for example `float half = 0.5;`)
- `boolean`: Booleans store a binary value of either `true` or `false`. When you declare a Boolean you must use the appropriate keyword: `bool isOn = true;`. Tip for using Booleans, name your variables like yes or no questions.
- `char`: Chars are a single characters, for example a b c 1 2 3 are all individual characters. Capital letters are considered separate characters from lowercase characters as well. When declaring a character in Processing you must use single quotes, (`char letter = 'a';`)
- `string`: Strings are a collection of characters all stored in one variable, and they are declared with double quotes. (`string name = "Peter";`)

You will frequently want to change the value of a variable as time goes on. For example if we have variable x that we previously declared as 5, but we want it to now be ten we could write some code like this
```
int x = 5;
x = 10;
```

Notice that when we re-assign x we don't use the integer keyword again. This is because we don't want to declare a new variable x, but instead use the prexisting one. If we were to use x again processing would give us an error. Alternatively we could add 5 to x to get 10 as well.
```
int x = 5;
x = 5 + x;
```

### Basic Arithmatic
Most programming languages support arithmatic like we did above with `x = 5 + x;`. In this case both **=** and **+** are what we refer to operators, since they perform specific opertaions on the data surrounding them. Here are some of the basic math operaters in Processing (most of them work just like regular math).

- `=`: Sets a variable on the left side to equal a value on the right. `x = 10;`
- `+`: Adds the values on both sides togehter. `5 + 5` will give us 10.
- `-`: Subtracts the value on the right side from the value on the left side. `3 - 2` will give us 1
- `*`: Multiplies the values on both sides togehter. `5 * 5` will give us 25.
- `/`: Divides the value on the right side by the value on the left side. `6 / 2` will give us 3

Math in Processing also works with order of operations like regular math problems, so if you want to add something before you multiply, you will need to wrap it in parenthesis. `3 + 2 * 5` will give us 13, while `(3 + 2) * 5` will give us 25.

### Functions
Sometimes we want to do the same thing a bunch of times in slightly different ways. For that in programming we have **functions**. We've already been using functions, `size(width, height); ellipse(x, y, width, height); stroke(r, g, b);` are all functions built in to Processing. It's really easy to create your own, if we want to make a function that strokes two circles when we call it, we would write it like this. An important note for Processing, from now on we will be running our main code in another function called setup, this function is called by Processing itself once all of the code has been compiled.
```
void setup() {
  size(640, 480);
  strokeTwoCircles();
}

void strokeTwoCircles() {
  int size = 20;

  stroke(255, 0, 0);
  ellipse(100, 100, size, size);

  stroke(255, 0, 0);
  ellipse(130, 100, size, size);
}
```
Then when we want to call that function we just run `strokeTwoCircles();`. Make sure you have your function definition before you try to call it!

If we want to make a function that can draw the same thing based on a size we pass in, we just add a variable to the function declaration inside of the parenthesis. 
```
void function strokeTwoCircles(int size) {
  stroke(255, 0, 0);
  ellipse(100, 100, size, size);

  stroke(255, 0, 0);
  ellipse(130, 100, size, size);
}
```
When you call this function make sure you remember to pass in an int for the size `strokeTwoCircles(10);`

These are called the parameters of the function, and you can have as many as you want. Now let's make it so we can change the x and y position of the circles by adding more parameters.
```
void function strokeTwoCircles(int x, int, y, int size) {
  stroke(255, 0, 0);
  ellipse(x, y, size, size);

  stroke(255, 0, 0);
  ellipse(x + size + 10, y, size, size);
}
```
And call this with `strokeTwoCircles(100, 100, 10);`. Notice that in the second call for drawing the ellipse I have some math for setting the circle's position to be 10 pixels to the right of the first circle, you can do your math inside of function calls!

### Make Drawing Trees Easier
Using the code you previously wrote before, write a function that allows you to draw trees of varying position, shape, and color based on the parameters that you define like we did above.
