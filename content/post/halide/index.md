+++
highlight = true
math = false
date = "2017-07-10T10:09:32-04:00"
title = "A brief overview of Halide"
tags = ["halide"]

[header]
  caption = ""
  image = ""

+++
<!--more-->
*THIS PAGE IS UNDER CONSTRUCTION*

Here I will talk about Halide

In order to learn Halide, let's start with a simple example and see how Halide 
will produce the code for that example

```C++
Var x,y
Func grad;
Target target = get_host_target();
grad(x,y) = x + y;
Halide::Image<int32_t> output = gradient.realize(100, 200);
```

Now let's see how the Halide will interpret this code. The first three lines
are nothing but defining Var and Funcs and the target where we will generate the
code. On line number 3, the update for the Func grad is written. This is where Halide
creates and initializes the data-structures for the functional specification to 
be later compiled into a C/C++ code. Note that the program that you are writing
is in itself in C++, and that is the beauty of Domain Specific Languages. You can
crate an language from say C++ and then use that to generate efficient C++ code. So, as 
we all know the operator () has a highest precedence than = operator and both () and =
operators of class Func have been overloaded. That means first the () operators on 
both LHS and RHS will be called and then the = operator will be called for the datatype
returned by grad(x,y). As () operator with arguments as std::vector of Vars is overladed
in Func.cpp and creates a FuncRef, the call to grad(x,y) returns a FuncRef object. As + has 
higher predence than =, so + will be evaluated on x and y, both of which are Vars and hence
+ operator. Now, if you search Var.cpp you will not find the overloaded operator + for Vars
and the reason is there is a cast operator "operator Expr()" which automatically casts Var
into object of type Expr and class Expr has overloaded + operator IROperator.h. This creates 
an Add IRNode. We will talk about IRNodes later, but the important thing is Add IRNode is also
of derived from Expr and hence + on two Vars casted to Expr will return an Expr. Hence now, 
= operator will be called on FuncRef object with the argument as an Expr. The overloaded =
operator for call FuncRef in Func.cpp which then calls func.define() or func.define_update() which is basically
but creating a Defintion for the current functional expression. Each functional expression
like the one line 4 has an associated definition with it. The first definition is called
"initial definition" and the rest are "update definitions". For example:

```
C(x,y) = 0.0f;  // initial definition
C(x,y) = A(x,y) + B(x,y) // update definition
``` 

Also each Func object has a Function func data member, which is used for book keeping and is
used in most of the places. Basically it is the internal representation of a Func. 
Hence func.define() in Function.cpp creates a Definition object for that particular function
expression and copies it into the contents data member of the Function object. Then the call to
= returns and we can execute the statement line 5 in our Halide code. At this point the
the Halide program is in the memory and have not been compiled yet. realize() method will
compile this program into a Halide IR which will then be lowered to LLVM IR or C. 
