---
layout: page
title: Examples
comments: false
permalink: /examples/
---

* content
{:toc}

## Introduction
Here are some simple examples utilizing the *icoBoards* two buttons and three LED's.

### Physical constraints
All Examples will use the same [*.pcf]({{ site.baseurl }}/glossary/#pcf) file, remember to comment in only those IO's the example is actually using or [arachne-pnr]({{ site.baseurl }}/glossary/#arachne-pnr) will fail:

<script src="https://gist.github.com/stylesuxx/98d91cac2c3b7dc0fcd6.js"></script>

The pins are taken from the [icoBoard schematic](http://downloads.amescon.com/icoboard.pdf).

### Building and flashing
All Examples may use the same Makefile for building and flashing the [Bitstream]({{ site.baseurl }}/glossary/#bitstream):

<script src="https://gist.github.com/stylesuxx/0bc913943fc388ee65da.js"></script>

To build and flash the example, simply run:
{% highlight bash %}
make example_flash
{% endhighlight %}

To build and flash the Bitstream, do not forget to run the clean target when you have changed the content of *example.v* or *example.pcf*:
{% highlight bash %}
make clean
{% endhighlight %}

## Basic

### Light LED with Button press
When pressing button *btn1* the LED *led1* should light up. When the button is released, the button should be off again.

**Solution:**
<script src="https://gist.github.com/stylesuxx/89837e196d55a8866e58.js"></script>
*Always* when the state of *btn1* changes, change the state of *led1* to the buttons current state.

This is what the *yosys show command* yields for our hardware description:

![Example Dot file](http://i.imgur.com/E6r67Gz.png)

### Toggle LED with Button press
When button *btn1* is pressed, LED *led1* should light up and stay lit, until button *btn1* is pressed again.

**Solution:**
<script src="https://gist.github.com/stylesuxx/b90892deb67587cfa0c5.js"></script>
In the *initial* block we initialize *led1* to be off. Every time the button *btn1* is pressed *led1* will takes its inverted state. When it was off before the button was pressed it will be on afterwards and vice versa.

![Example Dot file](http://i.imgur.com/8847fF6.png)

### Four LED states
When button *btn1* is pressed for the first time, *led1*, should light up and stay lit until the button is pressed a second time, then *led2* should light up. When pressing the button a third time, LED *led3* should be lit and all others off. Pressing the button a fourth time switches off all the LED's again.

**Solution:**
<script src="https://gist.github.com/stylesuxx/dba7a64ba853aff43aa6.js"></script>
A *register* with three bits is initialized to zero. The first time the button is pressed, the register is set to *001* and output to the three LED's every time the button is pushed, the register is shifted to the left by one bit, making the *1* bit wander to the left until it is all *0* again, then the process starts over.

![Example Dot file](http://i.imgur.com/LzGrskO.png)

## Intermediate

### Dimming a LED
*led1* should start dim and increase in brightness, until it has reached full brightness and should then start to dim again.

**Solution**
