# First Steps

## Building the Example
After [installing](installation.md) all needed tools it is time to run the example. Icoprog comes with an example to blink all the LED's. Connect to your RPi, change to the *icoprog* directory and build the bitstream for the example (if it is not built already):

```bash
make example.bin
```

This *make* target is responsible for the following tasks:

* [Synthesis](glossary.md#Synthesis) via [yosys](glossary.md#yosys): Takes *example.v* as input and synthesizes it to *example.[blif](glossary.md#blif)*
* [Pick and Route](glossary.md#Pick_and_Route) via [arachne-pnr](glossary.md#yosys): Takes *example.blif* and example.[pcf](glossary.md#pcf) and outputs a [Bitstream](glossary.md#Bitstream) in textual representation to *example.txt*
* Generate [Bitstream](glossary.md#Bitstream) with [icepack](glossary.md): Takes the readable *example.txt* and writes the [Bitstream](glossary.md#Bitstream) to *example.bin*

## Flashing the Example
Now that the [Bitstream](glossary.md#Bitstream) is available, it is time to flash it to the [FPGA](glossary.md#FPGA):

```bash
make example_flash
```

This will invoke [icoprog](glossary.md#icoprog) and flash *example.bin* to the icoBoard. You should now see the three LED's on the board blinking.
