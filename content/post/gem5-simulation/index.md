+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Gem5 Simulation Framework"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++
<!--more-->

This is an introduction tutorial to gem5 simulation framework 

# SimObject in gem5
```
/* src/sim/eventq.hh */
class EventManager {
  EventQueue * eventq;

  void schedule (Event &event, Tick when) { 
    eventq->schedule(&event);
  }
};

/* src/sim/sim_object.hh */
class SimObject : public EventManager {
  void init();
  void startup();
}

/* src/sim/eventq.hh */
EventQueue* getEventQueue(int index) {
  while(numMainEventQueues <= index) {
    numMainEventQueues++;
    mainEventQueue.push_back(new EventQueue(index));
  }
}

/* src/sim/sim_object.cc */
SimObject::SimObject(Params *p) : EventManager(getEventQueue(p->eventq_index)) {}

```

# Creating a SimObject

```
class VVADDXcel : SimObject {
  class VVADDXcelEvent : public Event {
    VVADDXcel *vvadd;
    void process() {
      if (!this->scheduled()) {
        vvadd->schedule(this, vvadd->clockEdge(1));
      }
      vvadd->tick();
    }
  }

  void tick() {
    /* Write the logic for posedge clock here */
  }
}
```

# Simulation using gem5
The configuration file se.py calls Simulation.run(). The Simulation.py in config/common/Simulation.py implements the run() method. This method calls m5.instantiate() and then calls benchCheckpoints() methods. benchCheckpoints() method calls m5.simulate(). The m5 package is located in src/python/m5. m5.initantiate() and m5.simulate() both are implemented in simulate.py. The m5.instantiate() first gets the root instance by calling objects.Root.getInstance(). The Root package is in src/sim/Root.py. m5.simulate() then calls obj.createCCObject(), obj.connectPorts(), obj.init(), obj.regStats(), obj.regProbePoints(), obj.regProbeListeners(), and obj.initState() in sequence on all the obj which are the descendants of the root ( i.e. all the objects ). Each of these methods is defined in the C++ or python version of SimObject and can be reimplemented by the class itself. The m5.simulate() calls the startup() method on all the descendants of the root. The descendants of the root are basically all the objects in the tree of hierarchies not just the ones that are specified in the config file.  These startup methods are implemented in the classes for e.g. there is a startup method in class MinorCPU, BaseCPU, SimpleThread etc. After calling the startup() methods it calls \_m5.event.simulate(). Don't know how but this simulate() method calls simulate() method in src/sim/simulate.cc. The simulate method calls a doSimLoop() method which has a while(1) loop that calls eventq->serviceOne() each iteration and the serviceOne method pops an event at the head of the queue and calls process() method of that event. 
