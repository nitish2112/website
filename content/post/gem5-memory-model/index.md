+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "A Tutorial on the Gem5 Memory Model"
tags = ["gem5"]

[header]
  caption = ""
  image = ""

+++
<!--more-->


Gem5 has following memory models (see src/mem/DRAMCtrl.py):

1)   DDR3_1600_8x8        : (1.6   x 8  x 8/8   = 12.8 GBps)
2)   HMC_2500_1x32        : (2.5   x 1  x 32/8  = 10.0 GBps)
3)   DDR3_2133_8x8        : (2.133 x 8  x 8/8   = 17.0 GBps)
4)   DDR4_2400_16x4       : (2.4   x 16 x 4/8   = 19.2 GBps)
5)   DDR4_2400_8x8        : (2.4   x 8  x 8/8   = 19.2 GBps)
6)   DDR4_2400_4x16       : (2.4   x 4  x 16/8  = 19.2 GBps)
7)   LPDDR2_S4_1066_1x32  : (1.066 x 1  x 32/8  =  4.3 GBps)
8)   LPDDR3_1600_1x32     : (1.6   x 1  x 32/8  =  6.4 GBps)
9)   GDDR5_4000_2x32      : (4.0   x 2  x 32/8  = 32.0 GBps)
10)  HBM_1000_4H_1x128    : (1.0   x 1  x 128/8 = 16.0 GBps)
11)  HBM_1000_4H_1x64

Each of these memories are derived from class DRAMCtrl in DRAMCtrl.py which
in turn is dervied from class AbstractMemory in AbstractMemory.py. DRAMCtrl is
a class for dram controller, but is being used to model the DRAM as well -- weird
but true.

class DRAMCtrl in dram_ctrl.hh has the following

```
class DRAMCtrl : public AbstractMemory {
  QueuedSlavePort port;

  class Bank {
    int openRow;
    int bank;
  }

  class Rank {
    int rank; // rank id
    vector<Command> cmdList; // List of commands issued
    vector<Bank> banks;
  }

  std::vector<Rank*> ranks;

  class DRAMPacket {
    Tick entryTime; // time when request entered the controller
    Tick readyTime; // time when request will leave the controller
    Packet* pkt;
    int rank, bank, row; // populated by row decoder
    int bankID;
    int size; // always less than or equal to DRAM bust size
  }

  std::deque<DRAMPacket*> readQueue;
  std::deque<DRAMPacket*> writeQueue;
  std::deque<DRAMPacket*> respQueue;

  // Actually do the DRAM access - figure out the latency it
  // will take to service the req based on bank state, channel state etc
  // and then update those states to account for this request.\ Based
  // on this, update the packet's "readyTime" and move it to the
  // response q from where it will eventually go back to the outside world.
  void doDRAMAccess();

  // When a packet reaches its "readyTime" in the response Q,
  // use the "access()" method in AbstractMemory to actually
  // create the response packet, and send it back to the outside world.
  void accessAndRespond();

  DRAMPacket* decodeAddr();

  // The memory schduler/arbiter - picks which request needs to
  // go next, based on the specified policy such as FCFS or FR-FCFS
  // and moves it to the head of the queue.
  bool chooseNext(std::deque<DRAMPacket*>& queue);

  // For FR-FCFS policy reorder the read/write queue depending on row buffer
  // hits and earliest bursts available in DRAM
  bool reorderQueue(std::deque<DRAMPacket*>& queue, ...);

  // Max column accesses (read and write) per row, before forefully closing it
  int maxAccessesPerRow;

  bool recvTimingReq(Packet* pkt);
  
  // Bunch of things requires to setup "events" in gem5
  // When event "respondEvent" occurs for example, the method
  // processRespondEvent is called;
  void processNextReqEvent();
  EventFunctionWrapper nextReqEvent;

  void processRespondEvent();
  EventFunctionWrapper respondEvent;
}
```

```
bool recvTimingReq (Packet* pkt) {
  int dram_pkt_count = ....; // calculate number of DRAM bursts a packet translates to
  if (pkt->isRead())
    Addr addr = pkt->getAddr();
    for i = 0...dram_pkt_count
      DRAMPacket* dram_pkt = decodeAddr(pkt);
      readQueue.push_back(dram_pkt);
      addr = (addr | (burstSize - 1)) + 1;
    schedule(nextReqEvent, curTick());
  else
    Addr addr = pkt->getAddr();
    for i = 0...dram_pkt_count
      DRAMPacket* dram_pkt = decodeAddr(pkt);
      writeQueue.push_back(dram_pkt);
      addr = (addr | (burstSize - 1)) + 1;
    accessAndRespond(pkt, frontendLatency);
    schedule(nextReqEvent, curTick());
}
```

```
void processNextReqEvent () {
  if (busState == READ) {
    if (!readQueue.empty()) {
      if (memSchedPolicy == Enums::frfcfs) // this is the default policy
        // reorderQueue will put the packt to be scheduled at the head of the queue
        bool found_read = reorderQueue(queue, switched_cmd_type ? tCS : 0);
      if (!found_read) return;
      DRAMPacket* dram_pkt = readQueue.front();
      doDRAMAccess(dram_pkt);
      readQueue.pop_front();
      respQueue.push_back(dram_pkt);
    }
  }
  else {
    if (memSchedPolicy == Enums::frfcfs) // this is the default policy
      bool found_write = reorderQueue(queue, switched_cmd_type ? tCS : 0);
    if (!found_write) return;
    DRAMPacket* dram_pkt = writeQueue.front();
    doDRAMAccess(dram_pkt);
    writeQueue.pop_front();
  }
}
```
```
PrIS Algorithm:

A seamless row buffer hit request: a request that can issue a column access
to an already activated row in the bank, without any further delay. 

A hidden bank prep request : a request that can overlap the current operation in 
other banks (in the same rank) and issue a request to activate or precharge a row 
in the requested bank. Among the hidden bank prep requests an FCFS policy is followed. 

A prepped row request : a request that needs to wait for the current column access 
to complete to the currently active row in a bank. Thus choosing a prepped row 
leads to a bubble in the pipeline of the scheduler where the request has to wait 
for the row to become available for a column access command.

Additionally, the PrIS algorithm picks a request in the priority order of
CPU seamless row buffer hit > CPU hidden bank prep > CPU prepped row

bool reorderQueue(std::deque<DRAMPacket*>& queue, Tick extra_col_delay) {

  Tick min_col_at = std::max(busBusyUntil - tCL + extra_col_delay, curTick());

  for (auto i = queue.begin(); i != queue.end(); i++) {
    DRAMPacket* dram_pkt = *i;
    if (dram_pkt->rankRef.isAvailable()) { // Rank is available
      // dram pkt row same as the open row
      if (dram_pkt->bankRef.openRow == dram_pkt->row) {
        // This a seamless request as this rquest can be handled before the next col event??
        if (dram_pkt->bankRef.colAllowedAt <= min_col_at) {
          selected_pkt = i; break;
        }
        else if (!found_hidden_bank && !found_prepped_pkt) {
          selected_pkt = i; found_prepped_pkt = true;
        }
      }
      // open row is not same as dram pkt row
      else if (!found_earliest_pkt) {
        hidden_bank_prep = ...
        if (...) found_hidden_bank = true
        if (hidden_bank_prep || !found_prepped_pkt)
          selected_pkt = i;
      }
    }
  }

  if (selected_pkt != queue.end()) {
    queue.erase(selected_pkt);
    queue.push_front(selected_pkt);
    return true;
  }
}
```
