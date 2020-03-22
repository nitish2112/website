+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "Adding custom statistics to gem5"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++
<!--more-->

This is a tutorial on how to add statistics in gem5. 

-- All the stats are defined in the namespace Stats. namespace Stats
   is spread across the following files:

```bash
   -- src/base/statistics.hh
   -- src/sim/stat\_control.hh
   -- src/base/stats/info.hh
   -- src/base/stats/output.hh
   -- src/base/stats/text.hh
   -- src/base/stats/type.hh
   -- src/python/pybind11/stats.cc
   -- sim/power/mathexpr\_powermodel.hh
```   
The schedStatEvent() function is used to dump statistics either all at once or
periodically. This is defined in src/sim/stat\_control.cc. schedStatEvent() 
creates a new StatEvent. The default values for when is curTick and repeat is 
0, so the StatEvent created by schedStatEvent executes in the same cycle and 
does not push itself to the event queue again when processed. The process() 
function of StatEvent calls Stats::dump() and Stats::reset() both of which 
are defined in base/statistics.hh. The dump() calls the constructor of 
dumpHandler() which is a variable "Handler dumpHandler" in the Stats namespace 
in the same file initialized to NULL. Same for reset and resetHandler(). The
dumpHandler and resetHandler variables are set using the call to registerHanlders()
also defined in the same file. 

When the gem5 system initantiates the objects by calling instantiate() method
in src/python/m5/simulate.py, in instantiate() method it calls stats.initSimStats()
which is defined in /src/python/m5/stats/__init__.py and calls 
_m5.stats.registerPythonStatsHandlers() which in turn is defined in 
src/sim/stat_register.cc and calls registerHandlers(pythonReset, pythonDump);
to regisetr the handlers we were talking about in the previous paragraph. 



<!--more-->

# Minor CPU


