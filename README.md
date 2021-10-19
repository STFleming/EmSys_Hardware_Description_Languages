# EmSys: Hardware Description Languages (HDLs)

Up until this point we have been learning about embedded systems from a SW perspective. However, let's start looking from a hardware perspective and actually describe some hardware.

## Early microprocessor design

<p align="center">
  <img src="imgs/Intel_C4004.jpeg" width="300" />
</p>
In 1971, the first commercial microprocessor, the Intel 4004, was developed. It was a revolutionary technology that placed a full 4-bit general purpose CPU into a small silicon chip, consisting of around 2300 transistors. Prior to the 4004 CPUs would have consisted of many chips, would have been much slower, and much more power hungry.  

<p align="center">
  <img src="imgs/4004-lajos-schematics.gif" width="600" />
</p>
The 2300 transistors that made up the Intel 4004 were all designed using a schematic diagram. Essentially, a large circuit was hand drawn showing how each individual transistor was connected to the other transistors. 

<p align="center">
  <img src="imgs/4004-masks-showing-fets-j3-.jpeg" width="500" />
</p>
From the schematic something called a mask was constructed. A mask shows the layout of the transistors on the silicon wafer and is used by the foundry during photolithography to construct the gates on the silicon wafer.

Doing such things manually by hand worked for a while, however, quite quickly the available transistor counts rapidly increased and the scalability of such approaches became an issue. Nowadays we have moved from microprocessors like the Intel 4004 consisting of 2300 transistors, to __Apples M1 Max that contains 57 Billion__.  

## Electronic design automation
Shortly after the release of the Intel 4004, in the mid 1970's, engineers started to work on using computers to aid in the layout and design of microprocessor circuits. They developed tools, such as place and route tools, that could be given a more abstract description of the circuit and generate schematics for how components should be connected together. However, it wasn't until the mid 1980's that this idea of using high-level languages to develop complex digitial circuits really started to take off with the creation of hardware modelling languages like Verilog and VHDL. 

Initially Verilog and VHDL, known as hardware description languages (HDLs), were high-level ways for engineers and chip designers to simulate and test their designs. They would describe the logical functionality of their circuits at a high-level, test it using simulations to ensure correct operation, and then went through the labor-intensive manual process of hand designing the schematic for the circuit. However, with dramatically increasing available transistors there was a growing call to automate the process of hand designing circuit schematics. 

An idea of logic synthesis, where HDLs were compiled down to gates and transistors, started to emerge in academia and industry. This move to logic synthesis thrust HDLs from being a small part of the silicon design process to being right at the forefront. While logic synthesis could not produce circuits that were are optimal or power efficient as hand drawn schematics, the productivity boost that they offered meant that they were quickly adopted. Nowadays, it is the primary way that digital circuits are described. 

## Let's describe some hardware

In this course we are exploring embedded systems. Crucial to embedded systems are constraints like:
* Timing and latency, how quickly can your system respond to events.
* Power, especially for battery operated devices.
* Performance.

Hardware plays a crucial role here, if we want to perform computation or control peripherals more efficiently then we need custom hardware rather than general purpose CPUs controlling everything. We've seen this with our ESP32 SoC, it contains custom hardware peripherals for common IoT operations, such as encryption, and communication protocols that have tight timing constraints.

Up until now we have looked at how we can write software, in C, to configure and interact with the memory-mapped hardware components. However, I want us also to explore the other side of this interface and develop some hardware and simulate it. For the next couple of lectures we will explore this, and in the lab sessions, will describe hardware in Verilog (a dominant hardware description language).  

<p align="center">
  <img src="imgs/verilator_overview.png" width="300" />
</p>
We will use an open-source hardware simulator, called [Verilator](https://www.veripool.org/verilator/). With Verilator we will be able to simulate some hardware that we have described in Verilog along with arduino-like code that is able to interact with it. By the end of the labs we will be able to construct hardware for something known as a pulse-width modulation (PWM) module, simulate it, and compare it to the PWM module on our TinyPico devices. 

I have tried to break down each of the concepts of verilog that I am teaching into short 5-10 minute videos. In the rest of this document you will find the links to those videos.

## Recap of logic gates

To start with we will only consider simpler digital circuits so that we can get used to the Verilog syntax and paradigms. Let's briefly recap some basic logic gates. 

<p align="center">
  <img src="imgs/AND.png" width="300" />
</p>

Above is an __AND__ gate. This gate has two inputs ``A`` and ``B`` and an output ``Q`` that is only high when the two inputs are both high.

<p align="center">
  <img src="imgs/OR.png" width="300" />
</p>

Above is an __OR__ gate. This gate has two inputs ``A`` and ``B`` and an output ``Q`` that is high when either of the two inputs are high.

<p align="center">
  <img src="imgs/NOT.png" width="300" />
</p>

Above is a __NOT__ gate. This gate has one inputs ``A`` and an output ``Q`` that is the opposite of whatever the input is.

<p align="center">
  <img src="imgs/LG_CHAINING.png" width="300" />
</p>

We can also of course chain these gates together, above we have an __AND__ gate that is followed by an __NOT__ gate. This is called a __NAND__ gate. 

<p align="center">
  <img src="imgs/NAND.png" width="300" />
</p>

However, sometimes we have a more convenient notation for showing this, where if we are inverting an input or an output we put a ``o`` next to it. For example, above represents a __NAND__ gate, the same as chaining a __NOT__ gate to the output of a __AND__ gate.

<p align="center">
  <img src="imgs/AND_3_input.png" width="300" />
</p>

We can also have more than two inputs for our gates, above shown a __3-input AND__ gate, where the output ``Q`` is only high when all three inputs are high.

<p align="center">
  <img src="imgs/modules.png" width="300" />
</p>

To aid design, we also often package gates up into modules, where we define some input ports and output ports and the internal logic is hidden. These modules can then be instantiated multiple times, making hierarchical design of circuits a bit cleaner.

## Simple in-n-out circuit

<p align="center">
  <img src="imgs/simple_in_n_out.png" width="300" />
</p>

Now that we have recapped some of the basics of logic gates and modular digital design, lets put them to use designing the simple circuit above in Verilog. Once we've finished our design we can then simulate it and examine the waveform of the signals in a waveform viewer. __Please watch the following video that describes how we can do this with Verilog and Verilator.__

<p align="center">
        <a href="http://www.youtube.com/watch?feature=player_embedded&v=fnoMnokP9mI
        " target="_blank"><img src="imgs/video_thumbnails/simple_in_n_out.png"
        alt="Lesson Video" width="510" height="360" border="10" /></a>
</p>

To build and run this example on the Linux lab machines (assuming you are in the root of this repository):
```
	cd lessons/1-simple_in_n_out
	make
```

Once the simulation completes you can then open ``gtkwave`` to look at the waveform.

```
	gtkwave wavedump.vcd
```

## Intermediate signals

5. Intermediate signals
	* How to define wires (or signals) to connect things together.
6. Bus signals
	* How to define arrays of wires (usually to represent numbers).
7. Procedural always_blocks
	* How to specify the logic in a more code-like behavioural fashion.

8. Recap!
