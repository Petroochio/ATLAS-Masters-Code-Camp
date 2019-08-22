# ATLAS Code Camp: Day 7

## Choose Your Own Project
Now that we've learned the basics, it's time for you to choose your first project on your own. This can be anything, just don't pick something too big since you only have till end of class Thursday. Here are some suggestions

- A data visualization that pulls from the web
- A simple audio visualizer
- A local multiplayer game with keyboard controls

Or whatever sounds fun to you!

## Timer Code
Here is the code from the timer example we went over in class
```
int prevTimestamp = 0;
int exTimer = 3000; // 3 seconds

void setup() {
  // Millis() returns the time in milliseconds since the app started
  prevTimestamp = millis();
}

void draw() {
  int currentTimestamp = millis();
  int deltaTime = currentTimestamp - prevTimestamp;

  if (exTimer <= 0) {
    println("times up!");
  } else {
    exTimer -= deltaTime;
  }

  // Do this last so the next frame knows the timestamp of the last frame
  prevTimestamp = currentTimestamp;
}
```
