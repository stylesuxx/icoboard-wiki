# Examples

## Introduction
Here are some simple examples utilizing the *icoBoards* two buttons and three LED's.

### Physical constraints
All Examples will use the same [*.pcf](glossary.md#pcf) file:

[gimmick:gist](98d91cac2c3b7dc0fcd6)

<script src="https://gist.github.com/nisrulz/11c0d63428b108f10c83.js"></script>

The pins are taken from the [icoBoard schematic](http://downloads.amescon.com/icoboard.pdf).

### Building and flashing
All Examples may use the same Makefile for builing and flashing the [Bitstream](glossary.md#Bitstream):

*TODO:* Add link to gist

## Light LED with Button press
### Problem
When pressing button *x* the LED *y* should light up.

### Solution

## Toggle LED with Button press
### Problem
When button *x* is pressed, LED *y* should light up and stay lit, until button *x* is pressed again.

### Solution

## Four LED states
### Problem
When button *x* is pressed for the first time, LED *1*, should light up and stay lit until the button is pressed a second time, then LED *2* should light up. When pressing the button a third time, LED *3* should be on all other off. Pressing the button a fourth time, all LED's should be off again.

### Solution
