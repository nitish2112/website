+++
# Recent Publications widget.
# Note: this widget will only display if `content/publication/` contains publications.

date = "2016-04-20T00:00:00"
draft = false

title = "Talks"
subtitle = ""
widget = "talks"

# Order that this section will appear in.
weight = 3

# Number of publications to list.
count = 10

# Show publication details (such as abstract)? (true/false)
detailed_list = true
+++

## **MatRaptor: A Sparse-Sparse Matrix Multiplication Accelerator Based on Row-Wise Product**

Sparse-sparse matrix multiplication (SpGEMM) is a computation kernel widely used in numerous
application domains. MatRaptor, a novel SpGEMM accelerator, is high performance and
highly resource efficient. Unlike conventional methods using inner or outer product as the
meta operation for matrix multiplication, our approach is based on row-wise product, which
offers a better tradeoff in terms of data reuse and on-chip memory requirements, and
achieves higher performance for large sparse matrices. We further propose a new
hardware-friendly sparse storage format, which allows parallel compute engines to access
the sparse data in a vectorized and streaming fashion, leading to high utilization of
memory bandwidth.

{{< youtube B8G5ZUDu4Yw >}}

## **Tensaurus: A Versatile Accelerator for Mixed Sparse-Dense Tensor Computations**

Tensor factorizations are powerful tools in many machine learning and data analytics applications. Tensors are often
sparse, which makes sparse tensor factorizations memory bound. In this talk, I present a hardware accelerator, Tensaurus,
that can accelerate both dense and sparse tensor factorizations. We co-design the hardware and a sparse storage
format, which allows accessing the sparse data in vectorized and streaming fashion and maximizes the utilization of
the memory bandwidth. We also extract a common computation pattern that is found in numerous matrix and tensor
operations and implement it in the hardware. 

{{< youtube cb2vFGbE_7M >}}

## **T2S-Tensor: Productively Generating High-Performance Spatial Hardware for Dense Tensor Computations**

We present a language and compilation framework for productively generating high-performance systolic arrays for 
dense tensor kernels on spatial architectures, including FPGAs and CGRAs. It decouples a functional specification from a spatial
mapping, allowing programmers to quickly explore various spatial optimizations for the same function. The actual implementation
of these optimizations is left to a compiler. Thus, productivity and performance are achieved at the same time.

{{< youtube 0NXys-RJ-zo >}}

