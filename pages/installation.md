# Installation
In order to use the icoBoard and flash a bitstream a couple of tools are required.

## Toolchain
The toolchain consist of three major components:

* [IceStorm Tools](https://github.com/cliffordwolf/icestorm) - Bitstream format documentation and tools
* [Arachne-PNR](https://github.com/cseed/arachne-pnr) - The place and route tool
* [Yosys](http://www.clifford.at/yosys/) - Verilog synthesis tool
* [icoprog](http://svn.clifford.at/handicraft/2015/icoprog) - icoBoard programming tool (needs to be run on the RPi)

Installation of the tools is described in detail on the [icestorm website](http://www.clifford.at/icestorm/) under the section *Where are the Tools? How to install?*.

## Raspberry Pi (RPi)
If you bought the [icoBoard bundle](http://icoboard.org/icoboard-bundle.html) you have everything preinstalled on the SD card to get you started - just hook up your input devices of choice and your HDMI screen, or simply connect via SSH.

### Manual installation
Information about manually installing, can be found in [Clifford's README for icoprog](http://svn.clifford.at/handicraft/2015/icoprog/README)

### Limitations
When working on the RPi
