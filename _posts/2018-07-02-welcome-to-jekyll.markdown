---
layout: default
title:  "Arduino, 8 x 8 Led Matrix, 2 Potentiometer, Nature of Code"
date:   2018-07-02 17:47:59 +0200
categories: jekyll update
---
Initialized the first row of the LED Matrix with Mover Objects set to ‘On’. The Mover Class is adapted form Dan Shiffmans `Nature of Code` [Video][video1], but doen't use acceleration. Instead the velocity is set direcly by the value of the two potentiometers. The analogRead gives each Mover Object its own value and so after some time the row pattern dissolves to some random organic pattern.

In the next test I will try to include acceleration.

<video  style="display:block; width:100%; height:auto;" autoplay controls loop="loop">
   <source src="https://quatlus.github.io/quatlus_blog/media/blog1led8x8_b.mp4" type="video/mp4" />
</video>
\\
---

`led_matrix_1.ino`
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

`Mover.cpp`
{% highlight ruby %}
#include "Mover.h"


Mover::Mover (float x_, float y_, float vx_, float vy_) {
  this->lx = x_;
  this->ly = y_;
  this->vx = vx_;
  this->vy = vy_;
  this->ax = 0;
  this->ay = 0;
}

void Mover::limit(float &var, float max_var) {
  if (var < -max_var) var = -max_var * .8;
  if (var > max_var) var = max_var * .8;

}

void Mover::update() {

  // acceleration changes velocity over time
  this->vx += this->ax;
  this->vy += this->ay;
  Mover::limit(this->vx, .5);
  Mover::limit(this->vy, .5);

  //velocity changes location over time
  this->lx += this->vx;
  this->ly += this->vy;
}

void Mover::addForce(float ax_, float ay_) {
  this->ax = ax_;
  this->ay = ay_;
}

void Mover::setVelocity(float vx_, float vy_) {
  this->vx = vx_;
  this->vy = vy_;
}

void Mover::edges() {
  if (this->lx  > 8) this->lx = 0;
  if (this->lx  < 0) this->lx = 7;
  if (this->ly  > 8) this->ly = 0;
  if (this->ly  < 0) this->ly = 7;
}

void Mover::display() {

}

int Mover::getx() {
  return round(this->lx);
}

int Mover::gety() {
  return round(this->ly);
}
{% endhighlight %}

`Mover.h`
{% highlight ruby %}
#include <Arduino.h>

class Mover {
  public:
    float lx; // location x
    float ly; // location y
    float vx; // velocity x
    float vy; // velocity y
    float ax; // acceleration x
    float ay; // acceleration y

  public:
    Mover (float x_, float y_, float vx_, float vy_);
    void update();
    void limit(float &var, float max_var);
    void setVelocity(float vx_, float vy_);
    void addForce(float ax_, float ay_);
    void edges();
    void display();
    int getx();
    int gety();
};
{% endhighlight %}

[video1]: https://www.youtube.com/watch?v=TQ_WZU5s_VA
