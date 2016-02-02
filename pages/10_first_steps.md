---
layout: page
title: First Steps
comments: false
permalink: /first-steps/
---

* content
{:toc}

## Building the Example
After [installing]({{ site.baseurl }}/installation/) all needed tools it is time to run the example. Icoprog comes with an example to blink all the LED's. Connect to your RPi, change to the *icoprog* directory and build the bitstream for the example (if it is not built already):


{% highlight bash %}
make example.bin
{% endhighlight %}

This *make* target is responsible for the following tasks:

* [Synthesis]({{ site.baseurl }}/glossary/#Synthesis) via [yosys]({{ site.baseurl }}/glossary/#yosys): Takes *example.v* [Verilog]({{ site.baseurl }}/glossary/#Verliog) hardware description file as input and synthesizes it to *example.[blif]({{ site.baseurl }}/glossary/#blif)*
* [Pick and Route]({{ site.baseurl }}/glossary/#place-and-route) via [arachne-pnr]({{ site.baseurl }}/glossary/#yosys): Takes *example.blif* and example.[pcf]({{ site.baseurl }}/glossary/#pcf) and generates a [Bitstream]({{ site.baseurl }}/glossary/#bitstream) in textual representation to *example.txt*, at this point you can still *cat* it and read it.
* Generate [Bitstream]({{ site.baseurl }}/glossary/#bitstream) with [icepack]({{ site.baseurl }}/glossary/#icepack): Takes the readable textual representation of the [Bitstream]({{ site.baseurl }}/glossary/#bitstream) (*example.txt*) and writes the [Bitstream]({{ site.baseurl }}/glossary/#bitstream) to *example.bin*

## Flashing the Example
Now that the [Bitstream]({{ site.baseurl }}/glossary/#bitstream) is available, it is time to flash it to the [FPGA]({{ site.baseurl }}/glossary/#fpga):

{% highlight bash %}
make example_flash
{% endhighlight %}

This will invoke [icoprog]({{ site.baseurl }}/glossary/#icoprog) and flash *example.bin* to the icoBoard. You should now see the three LED's on the board blinking.

## Dissecting the Source
Let's take a deeper look into the two source files used to build the [Bitstream]({{ site.baseurl }}/glossary/#bitstream).

### example.pcf
The physical constraints file contains the pin mappings to variables:

{% highlight bash %}
set_io clk R9
set_io led1 C8
set_io led2 F7
set_io led3 K9
{% endhighlight %}

The *led* variables are mapped to the pins they are connected to. Same goes with clk, which is the [FPGA's]({{ site.baseurl }}/glossary/#fpga) clock signal. You can verify and look up the pin identifiers and what they are connected to in the [schematic](http://downloads.amescon.com/icoboard.pdf).

### example.v
This may be the point where you should go through some [Verilog]({{ site.baseurl }}/glossary/#verliog) tutorials first. But let's look at it anyway and see if we can find out what is happening. The following description is comming from someone who never has seen [Verilog]({{ site.baseurl }}/glossary/#verliog) before, so bear with me.

{% highlight verilog %}
module top (input clk, output reg led1, led2, led3);
  parameter COUNTER_BITS = 26;
  reg [COUNTER_BITS-1:0] counter = 0;
  always @(posedge clk) begin
    counter <= counter + 1;
    {led1, led2, led3} <= counter[COUNTER_BITS-1 -: 3] ^ (counter[COUNTER_BITS-1 -: 3] >> 1);
  end
endmodule
{% endhighlight %}

The firs line seems to define a new module and set the used *IO* pins defined in *example.pcf* as in or output, so *clk* is an input. *led1*, *led2* and *led3* are set as output.

{% highlight verilog %}
module top (input clk, output reg led1, led2, led3);
{% endhighlight %}

The next line seems to initialize a variable called *COUNTER_BITS* with an integer value of 26, and the one after that seems to initialize a register with the name *counter* to an array of length *COUNTER_BITS* and initializes them with **0**. So we have an array capable of holding *26 bits* which are all zeroed out at the beginning:

{% highlight verilog %}
  parameter COUNTER_BITS = 26;
  reg [COUNTER_BITS-1:0] counter = 0;
{% endhighlight %}

The next line seems to define a function that is executed every time when a positive edge from the clock is detected and executes everything until *end*.

{% highlight verilog %}
  always @(posedge clk) begin
    ...
  end
{% endhighlight %}

Alright, so the part that lets the LED's blink seems to be in the *always* block. The first line is pretty forward, increase the counter by one:

{% highlight verilog %}
    counter <= counter + 1;
{% endhighlight %}

So on every positive edge, our 26 Bit counter is increased by one, lets check out what *counter* contains:

{% highlight text %}
Before 1st positive edge: 00000000000000000000000000
1st edge:                 00000000000000000000000001
2st edge:                 00000000000000000000000010
3rd edge:                 00000000000000000000000011
4th edge:                 00000000000000000000000100
...
67108864th edge:          11111111111111111111111111
67108865th edge:          00000000000000000000000000
67108866th edge:          00000000000000000000000001
...
{% endhighlight %}

So the counter will overflow every *67108865th* time back to the initial state.

Good, so the functionality to flash the LED's has to happen in the second line of the *always* block:

{% highlight verilog %}
    {led1, led2, led3} <= counter[COUNTER_BITS-1 -: 3] ^ (counter[COUNTER_BITS-1 -: 3] >> 1);
{% endhighlight %}

This seems to assign the LED's a value from our counter array, let's take a look in detail:

{% highlight verilog %}
counter[COUNTER_BITS-1 -: 3] ^ (counter[COUNTER_BITS-1 -: 3] >> 1)
{% endhighlight %}

For this part we need to know what **^**, **>>** and **[COUNTER_BITS-1 -: 3]** do.
It is reasonable to assume that **>>** is the *right shift* operator, shifting the *counter* array to the right by one Bit, but in this case it only shifts the first three bits because of *[COUNTER_BITS-1 -: 3]*. Further we can assume, that **^** is the bit wise *xor* operator.

This means:

{% highlight text %}
1st positive edge:
original counter: 000 00000000000000000000001
shifted counter:  000 00000000000000000000000
xored:            000 00000000000000000000001
---------------------------------------------
LED's on: none

2nd positive edge:
original counter: 000 00000000000000000000010
shifted counter:  000 00000000000000000000000
xored:            000 00000000000000000000010
---------------------------------------------
LED's on: none

...

8388608th positive edge:
original counter: 000 11111111111111111111111
shifted counter:  000 11111111111111111111111
xored:            000 00000000000000000000000
---------------------------------------------
LED's on: none

8388609th positive edge:
original counter: 001 00000000000000000000000
shifted counter:  000 00000000000000000000000
xored:            001 00000000000000000000000
---------------------------------------------
LED's on: 3

8388610th positive edge:
original counter: 001 00000000000000000000001
shifted counter:  000 00000000000000000000001
xored:            001 00000000000000000000000
---------------------------------------------
LED's on: 3

...

16777216th positive edge:
original counter: 001 11111111111111111111111
shifted counter:  000 11111111111111111111111
xored:            001 00000000000000000000000
---------------------------------------------
LED's on: 3

16777217th positive edge:
original counter: 010 00000000000000000000000
shifted counter:  001 00000000000000000000000
xored:            011 00000000000000000000000
---------------------------------------------
LED's on: 2+3

...

25165824th positive edge:
original counter: 010 11111111111111111111111
shifted counter:  001 11111111111111111111111
xored:            001 00000000000000000000000
---------------------------------------------
LED's on: 2+3

25165825th positive edge:
original counter: 011 00000000000000000000000
shifted counter:  001 00000000000000000000000
xored:            010 00000000000000000000000
---------------------------------------------
LED's on: 2

...

33554432th positive edge:
original counter: 011 11111111111111111111111
shifted counter:  001 11111111111111111111111
xored:            010 00000000000000000000000
---------------------------------------------
LED's on: 2+3

33554433th positive edge:
original counter: 100 00000000000000000000000
shifted counter:  010 00000000000000000000000
xored:            110 00000000000000000000000
---------------------------------------------
LED's on: 1+2

...

41943040th positive edge:
original counter: 100 11111111111111111111111
shifted counter:  010 11111111111111111111111
xored:            110 00000000000000000000000
---------------------------------------------
LED's on: 1+2

41943041th positive edge:
original counter: 101 00000000000000000000000
shifted counter:  010 00000000000000000000000
xored:            111 00000000000000000000000
---------------------------------------------
LED's on: 1+2+3

...

50331648th positive edge:
original counter: 101 11111111111111111111111
shifted counter:  010 11111111111111111111111
xored:            111 00000000000000000000000
---------------------------------------------
LED's on: 1+2+3

50331649th positive edge:
original counter: 110 00000000000000000000000
shifted counter:  011 00000000000000000000000
xored:            101 00000000000000000000000
---------------------------------------------
LED's on: 1+3

...

58720256th positive edge:
original counter: 110 11111111111111111111111
shifted counter:  011 11111111111111111111111
xored:            101 00000000000000000000000
---------------------------------------------
LED's on: 1+3

58720257th positive edge:
original counter: 111 00000000000000000000000
shifted counter:  011 00000000000000000000000
xored:            100 00000000000000000000000
---------------------------------------------
LED's on: 1

...

67108864th positive edge:
original counter: 111 11111111111111111111111
shifted counter:  011 11111111111111111111111
xored:            100 00000000000000000000000
---------------------------------------------
LED's on: 1

67108865th positive edge:
original counter: 000 00000000000000000000000
shifted counter:  000 00000000000000000000000
xored:            000 00000000000000000000000
---------------------------------------------
LED's on: none
{% endhighlight %}

Now it also should be clear what **[COUNTER_BITS-1 -: 3]** does, it "selects" the first three bits and writes them to the LED's. But is this true? Well that is easy to confirm, just flash the example and watch the LED's.

There are basically *eight* different states the example loops through, which makes sense - there are 3 LED's and all different permutations are 2^3 = 8. Each state *8388608 positive edges* long.

{% highlight text %}
State 0: LED's on: none
State 1: LED's on: 3
State 2: LED's on: 2+3
State 3: LED's on: 2
State 4: LED's on: 1+2
State 5: LED's on: 1+2+3
State 6: LED's on: 1+3
State 7: LED's on: 1
{% endhighlight %}

But *why* do we need *26 bits*? Because three bits are displayed on the LED's, leaving 23 for the count of the duration of a single state, and we all love the number **23**.

This is in fact something you can now easily try for yourself. If you increase the number of bits, each state will hold on for a longer time. To be exact, each state is held for (n-3)^2 positive edges.

Same thing goes with decreasing until a point where the state changes so fast, that you can not follow it with your eye and all LED's seem to be on all the time.

## Interpreting the graphical representation
With [yosys]({{ site.baseurl }}/glossary/#yosys) you have the possibility to show a graphical representation of the design. You can use the [interactive yosys console]({{ site.baseurl }}/yosysjs/) to test this.

Create two files:

* *example.v* with the following content:

{% highlight verilog %}
module top (input clk, output reg led1, led2, led3);
  parameter COUNTER_BITS = 26;
  reg [COUNTER_BITS-1:0] counter = 0;
  always @(posedge clk) begin
      counter <= counter + 1;
      {led1, led2, led3} <= counter[COUNTER_BITS-1 -: 3] ^ (counter[COUNTER_BITS-1 -: 3] >> 1);
  end
endmodule
{% endhighlight %}

* *example.ys* with the following content:

{% highlight text %}
# a simple yosys.js example. run "script example.ys".
design -reset
read_verilog example.v
proc
opt
show
{% endhighlight %}

Then run the yosys script:

{% highlight bash %}
script example.ys
{% endhighlight %}

This should generate a dot file that looks like this:

![show.dot](http://i.imgur.com/jtCII6T.png)
