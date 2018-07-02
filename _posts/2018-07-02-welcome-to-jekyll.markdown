---
layout: post
title:  "Arduino, 8 x 8 Led Matrix, 2 Potentiometer, Nature of Code"
date:   2018-07-02 17:47:59 +0200
categories: jekyll update
---
Initialized the first row of th LED Matrix with Mover Objects set to 'On'. The Mover Class is adapted form Dan Shiffmans `Nature of Code` [Video][video1], but dont uses Accelleration. Instead the Velocity is set direcly by the value of the two Potentiometers. The analogeRead gives each Mover Object its own value and so after some time the row pattern dissolves to some random organic "clouds" or "rain" or "fire" pattern.

In the next Test I will try to include Accelleration.

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

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[video1]: https://www.youtube.com/watch?v=TQ_WZU5s_VA
