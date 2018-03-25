# Water Ripples

For this tutorial, we're going to learn how to create interactive water ripples that look like this:

![ripple](ripples.png)


## Step 1: Drawing a circle with points.

In order to create a ripple that has an uneven stroke, we need a to draw a circle with points that we can manipulate. As we learnt from last week, the x and y coordinates of the points along a circle are:

```js
float rad;          //the radius of the circle
float angle;        //the angle between the origin and the point

x = rad * sin(angle);
y = rad * cos(angle);
```
In order to make the numbers a little more manageable, we can convert `angle` from degrees into radians:

```js
x = rad * sin(radians(angle));      //this converts your input in degrees into radians.
y = rad * cos(radians(angle));
```

We can now use these x and y coordinates to create points at every degree:

```js
x1 = rad * sin(radians(0));
y1 = rad * cos(radians(0));
vertex(x1, y1);

x2 = rad * sin(radians(1));
y2 = rad * cos(radians(1));
vertex(x2, y2);

.
.
.

x360 = rad * sin(radians(359));
y360 = rad * cos(radians(359));
vertex(x359, y359);
```

By enclosing all these points within `beginShape()` and `endShape()` tags, we can draw a circle. We can also use a loop to go through every degree easily:

```js
beginShape();
for (float angle = 0; angle < 360; angle ++) {
  x = rad * sin(radians(angle));
  y = rad * cos(radians(angle));
  vertex(x, y);
}
endShape(CLOSE);
```

Let's see what we've got so far:

```js
float rad = 100;
float x, y;

void setup() {
  size(500, 500);
}

void draw() {
  translate(width/2, height/2);       //moving the circle to the middle.
  background(0);
  noFill();
  stroke(255);

  beginShape();
  for (float angle = 0; angle < 360; angle ++) {
    x = rad * sin(radians(angle));
    y = rad * cos(radians(angle));
    curveVertex(x, y);                //we're using curveVertex here so that the circle is round!
  }  
  endShape(CLOSE);
}
```


## Step 2: Make the ripple expand.

Now that we've got our circle, we can make it expand by increasing its radius, `rad`:
```js
float increment = 0;         //we can make a variable that increases the radius
float initialRad = 5;       //and another variable that holds the initial value of the radius
float rad;
.
.
.
beginShape();
for (float angle = 0; angle < 360; angle ++) {
  rad = initialRad + increment;
  x = rad * sin(radians(angle));
  y = rad * cos(radians(angle));
  curveVertex(x, y);
}
endShape(CLOSE);

increment++;
```

We can reset `increment` upon clicking the mouse by adding a `mousePresed()` function:

```js
void mousePressed() {
  increment = 0;
}
```

## Step 3: Make the ripple expand from your mouse location.

In step 1, we translated the sketch to the middle by using `translate(width/2, height/2)`. Let's see if we can set that location to be `(mouseX, mouseY)` when you click!

First, let's create a variable for storing your `mousePressed()` location:

```js
float xPos, yPos;
.
.
.
void draw() {
  translate(xPos, yPos);    //we will translate the sketch based on this position
  .
  .
  .
}
```

Now we can store your mouse's position by going into `mousePressed()` and assigning them the right values:

```js
void mousePressed() {
  increment = 0;
  xPos = mouseX;
  yPos = mouseY;
}
```

## Step 4: It's time to make a wavy ripple!

Now that we've got our expanding circle, we can start to manipulate the individual points. Instead of using `random()` to add a little distortion to the points, we're gonna use `noise()` instead. `noise()` produces a more natural, harmonic succession of random numbers than `random()`.

`noise()` requires an input coordinate to distort around, and it produces a value between 0.0 and 0.1. So in order to distort the stroke of our ripple, we need to distort it's radius. 

```js
beginShape();
for (float angle = 0; angle < 360; angle ++) {   
  rad = initialRad + increment + noise(x)*10;      //we will distort around the x coordinate    
  x = rad * sin(radians(angle));                   //and multiply it so it's more obvious.    
  y = rad * cos(radians(angle));
  curveVertex(x, y);
}
endShape(CLOSE);
```

You should now see a very, very distorted line. We can increase the increments in `angle` to make it a little smoother:
```js
beginShape();
for (float angle = 0; angle < 360; angle += 10) {   
  .
  .
  .
}
endShape(CLOSE);
```

## Step 5: Time to make it look like water!

Now we just need to change the way it looks to create the desired watery effect. 

First, we need to make the lines fade out. To do this, we need to draw the background a little differently:

```
void setup() {
  size(500, 500);
  background(0);      //we draw the initial background here
}

void draw() {
  fill(0, 10);        //we draw a semi transparent black rectangle every frame on draw.
  rect(0, 0, width, height);
  translate(xPos,yPos);
  .
  .
  .
```

```js
.
.
.
void draw() {
  translate(xPos, yPos);      
  background(0);
  noFill();
  strokeWeight(12);             //thicker strokes to make them overlap a little
  stroke(random(120, 170), 210, 245, 25);
  .
  .
  .
}
```

