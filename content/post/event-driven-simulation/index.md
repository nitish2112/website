+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Computer Architectural Simulation Techniques"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++
<!--more-->

This is an introduction tutorial on different types of simulation techniques and a brief overview of event driven simulators.

# **Simulators**
A simulator is a software performance model of a processor architecture which runs on a host machine. Simulation is cheap and much more accurate than analytical models. 

## **Metrics for how good a simulator is**
1) How accurate the simulation model is as compared to real hardware.

2) How long does it take to develop the simulator, 

3) How much time does the simulator take to simulate the design, and 

4) How much coverage the simulator provides

# **Different Simulation Techniques**

## **Functional Simulation**

Functional simulators only model the functional characteristics of a design. The instructions are simulated one at a time more of like an interpreter. These simulators are called ISA simulators. They are used to validate the correctness of a design rather than its performance characteristics. Some examples of ISA simulators would be spike for RISCV, Pydgin, SimpleScalar's sim-safe etc.

## **Trace Driven Simulation**

A trace driven simulator takes program instructions and address traces and supplies them to micro-architectural simulator. Trace driven simulator separates functionality from timing. They require the user to store the trace files which can grow really large. However, trace driven simulators are not good in modelling multithreaded workloads as they cannot model the interaction between multiple threads.

## **Execution Driven Simulation**

Execution driven simulators combine timing and functionality together. Depending upon the integration of functional and timing components execution driven simulators are classified as follows:

1) **Integrated** -- Timing and functional components are tightly coupled.

2) **Timing Directed** -- The functional model keeps track of the architectural state like register file, memory etc and timing model gets information from the functional model regarding various timing events like whether a load is a cache hit or miss and then it can account for the timing of that event. 

3) **Functional First Simulation** -- Functional first simulators are more like Trace driven simulators. A functional simulator produces the traces which are at the same time fed to the timing simulator. The advantage of functional first simulator over trace driven simulators is that there is no need to store traces for functional first simulators.

4) **Timing First Simulation** -- In timing first simulators, the timing simulator models the architectural features ( register file, memory etc ). When the timing simulator commits an instruction the functional model checks it with the functional simulation. If there are any deviations, the functional simulator repairs the timing simulator by reloading the appropriate architectural state. 

# **Event driven simulation**

Event driven simulation falls more into the category of timing directed simulations. Event driven simulators consist of an event queue ( priority queue ) which is checked each clock cycle to see if any event is scheduled. Events and event queues model the timing part of the simulator. Whenever an event fires, it executes the process function that the object ( whose event fired ) has assigned to this particular event. Objects and their process methods constitute the functional part of the simulator.   




