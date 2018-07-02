---
layout: default
title:  "Arduino, 8 x 8 Led Matrix, 2 Potentiometer, Nature of Code"
date:   2018-07-02 17:47:59 +0200
categories: jekyll update
---
Initialized the first row of the LED Matrix with Mover Objects set to ‘On’. The Mover Class is adapted form Dan Shiffmans `Nature of Code` [Video][video1], but doen't use acceleration. Instead the velocity is set direcly by the value of the two potentiometers. The analogRead gives each Mover Object its own value and so after some time the row pattern dissolves to some random organic “clouds”, “rain” or “fire” pattern.

In the next test I will try to include acceleration.

{% highlight ruby %}
#include "LedControl.h"
#include "Mover.h"

LedControl lc = LedControl(12, 10, 11, 2);

const int8_t size_mleds = 8;

Mover mleds[size_mleds] {
  { 0, 0, 0, 0 }, { 1, 0, 0, 0 }, { 2, 0, 0, 0 }, { 3, 0, 0, 0 },
  { 4, 0, 0, 0 }, { 5, 0, 0, 0 }, { 6, 0, 0, 0 }, { 7, 0, 0, 0 }
};

float poti_vertical, poti_horizontal;

int delayTime = 1;
unsigned long old = millis();

int delayTimeloop = 10;
unsigned long oldloop = millis();
int i;

void setup() {

  lc.shutdown(0, false);
  lc.setIntensity(0, 6);
  lc.clearDisplay(0);

  // analogReference(INTERNAL);
  int i = 0;
}

void loop() {

  poti_vertical = map(analogRead(A4), 0, 1023, -5, 5 );
  poti_horizontal = map(analogRead(A5), 0, 1023, -5, 5 );
  poti_vertical /= 10;    // range [-0.5, 0.5]
  poti_horizontal /= 10;  // range [-0.5, 0.5]

  //Serial.println(analogRead(A4));
  //Serial.println(poti_vertical);
  //Serial.println("---------------------");

  mleds[i].setVelocity(poti_horizontal, poti_vertical);

  mleds[i].update();
  mleds[i].edges();

  lc.setLed(0, mleds[i].ly, mleds[i].lx, 1);

  i++;
  if (i >= size_mleds) {
    i = 0;
    lc.clearDisplay(0);
  }

}
{% endhighlight %}


[video1]: https://www.youtube.com/watch?v=TQ_WZU5s_VA
