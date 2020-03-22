+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Gem5 miscellaneous"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++
<!--more-->

# Adding an option to gem5

Say we want to add option "xyz" to our simulator

1) Add it to configs/common/Options.py: In addCommonOptions() function add
   parser.add\_option("--xyz", action="store\_true")

2) Add this option as a parameter in base cpu class. In src/cpu/BaseCPU.py
   add this to the class BaseCPU:
  
   xyz = Param.Bool(False, "Enable line trace")

3) Add xyz as a public class member in base class of cpu in src/cpu/base.hh
   
   bool xyz;

4) Add support for xyz in constructor of base class for cpu in src/cpu/base.cc

   xyz               =  params()->xyz;

5) Pass the option from the config script to the cpu class. After instantiating
   cpus i.e.

   system = System(cpu = [CPUClass(cpu_id=i, num_cores=np)
                          for i in xrange(np)] .... ) 
  
   etc, do 
   
   system.cpu[0].xyz = options.xyz

6) Now you can use the true/false boolean xyz to do conditional operation in 
   you cpu member functions as:

   if ( xyz ) {  .... } else { ... }
  
   
We have now added a new option to gem5.



