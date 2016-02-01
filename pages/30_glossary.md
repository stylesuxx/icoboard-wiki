---
layout: page
title: Glossary
comments: false
permalink: /glossary/
---

* content
{:toc}

## A

### arachne-pnr
[Place and Route](#Place-and-Route) tool by Cotton Seed available on [github](https://github.com/cseed/arachne-pnr). It accepts a [netlist](#netlist) in [blif](#blif) format and outputs a textual representation of the [Bitstream](#bitstream).

## B

### Bitstream
As the name suggests, the *Bitstram* is a stream of bits. It describes the configuration data loaded into an [FPGA](#fpga).

### blif
File extension for the [Berkley Logic Interchange format](https://www.ece.cmu.edu/~ee760/760docs/blif.pdf) used to describe a logic-level circuit in readable form.

## F

### Flip Flop
Saves a single bit as long as power is supplied.

### FPGA
Field programmable gate array.

## I

### iCE40
An [FPGA](#fpga) produced by [Lattice](http://www.latticesemi.com/Products/FPGAandCPLD/iCE40.aspx) with 7680 programmable logic cells and the heart of the icoBoard.

### icepack
A tool from the [icestorm](#icestorm) tool suite to generate a [Bitstream](#bitstram) from the readable textual representation of the [Bitstream](#bitstram).

### icoprog
A tool from the [icestorm](#icestorm) tool suite to flash a [Bitstream](#bitstram) to the [FPGA](#fpga).

### icestorm
A [toolsuite](http://www.clifford.at/icestorm/) by Clifford Wolf and Mathias Lasser aimed at documenting the [Bitstream](#bitstram) of the [iCE40](#ice40) and providing tools for analyzing and creating [Bitstreams](#bitstram).

## L

### PLB
*Programmable Logic Block* consists of eight [logic cells](#logic-cell) in the iCE40.

### Logic Cell
A *logic cell* consists of one [LUT](#lut) and a [Flip Flop](#flip-flop) is a logic cell in the iCE40.

### Logic Gate
Performs a logical operation on one ore more inputs. Has only one logical output.

### LUT
Look Up Table, the building block for [logic gates](#logic-gate). The LUT's on the iCE40 have four inputs and can therefore store a truth table of size 2^5 = 32.

## N

### Netlist
A description of the connectivity of an electronic circuit.

## S

### Synthesis
*Synthesis* is the generation of a [Bitstream](#bitstream) from a hardware description like [VHDL](#vhdl) or [Verilog](#verilog).

## P

### pcf
File extension for *physical constraints file*.

### Place and Route
Place and route describes several processes where the [netlist](#netlist) elements are physically placed and mapped to the [FPGA](#fpga)'s physical resources.

## R

### RPi
Short for Raspberry Pi, in context of the icoBoard this is alway the **Raspberry Pi Model 2 B**, which is used as the programmer for the icoBoard.

## V

### Verilog
A Hardware description language.

### VHDL
A Hardware description language.

## Y

### yosys
Open [Synthesis](#Synthesis) suite by Clifford Wolf on [github](https://github.com/cliffordwolf/yosys) or [the website](http://www.clifford.at/yosys/).
