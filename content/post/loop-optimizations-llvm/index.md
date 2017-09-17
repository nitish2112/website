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

This is an introduction tutorial on Loop Analysis Passes in LLVM
<!--more-->

class Loop is defined in Analysis/LoopInfo.h. The main data members of Loop class are :
1) ParentLoop: parent loop
2) SubLoops: List of loops contained within this loop
3) Blocks: List of basic blocks in the loop

class BasicBlock is defined in IR/BasicBlock.h has following data-members:
1) InstList: List of instructions
2) Parent: Function in which this basic blocks resides

Each Pass also has its own class and whatever new information the passes extract are stored in
the object of that pass only. For example DominatorTreeWrapperPass has following members:
1) DominatorTree DT;

Now, if we see the class DominatorTree it has following members:
1) DomTreeNodeMapType DomTreeNodes;
2) DomTreeNodeBase<NodeT> * RootNode;
3) std::vector<NodeT *> Vertex;
4) DenseMap<NodeT *, NodeT *> IDoms;

Similarly LoopInfoWrapperPass (Analysis/LoopInfo.h) has following data members:
1) LoopInfo LI

ScalarEvolutionWrapperPass (Analysis/ScalarEvolutionWrapperPass.h) has following members:
1) ScalarEvolution * SE;

ScalarEvolution (Analysis/ScalarEvolution.h) has following members:
1) Function
2) TargetLibraryInfo &TLI;
3) AssumptionCache &AC;
4) DominatorTree &DT;
5) LoopInfo &LI;
6) ... and others

Any pass is derived from class Pass (include/llvm/Pass.h) and has at least these data members inherited from Pass:
1) AnalysisResolver * Resolver;  // Used to resolve analysis
2) const void * PassID;
3) PassKind Kind;

Neither of FunctionPass, BasicBlockPass or ModulePass add any extra data-members.

LLVMContext is the class in IR/LLVMContext.h which has following data members:
1) LLVMContextImpl * const pImpl;

LLVMContextImpl is defined in lib/IR/LLVMContextImpl.h and has following data members:
1) OwnedModules: modules in the context
2) 
