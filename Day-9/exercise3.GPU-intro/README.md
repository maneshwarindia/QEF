# Exercise 3: (very) basic concepts about GPUs

This exercise is meant to provide some basic practical notions about how GPU acceleration works.

------------------------------------------------------------------------

Let's go to the exercise3 folder in your $CINECA_SCRATCH directory

~~~~~{.bash}
cd $CINECA_SCRATCH  
cd Day-9/exercise3.GPU-intro/
~~~~~

------------------------------------------------------------------------

The source file `code_cpu.f90` is a minimal program to perform a matrix-matrix product on the CPU using the DGEMM subroutine from the BLAS libraries.

In order to compile the code, you first purge all modules with

	module purge

and then load the nvfortran compiler with 

	module load autoload hpc-sdk 

------------------------------------------------------------------------

Then, you can compile the code appending the -lblas flag in order to link the BLAS libraries. 
In this case you will be using the BLAS libraries provided with the `nvfortran` compiler in the `hpc-sdk` package.

	nvfortran -o code_cpu.x code_cpu.f90  -lblas 

------------------------------------------------------------------------

The source file `code_gpu.f90` does the same calculation as `code_cpu.f90`, but on the GPU, using the cuDGEMM subroutine from the cuBLAS libraries.
In order to compile the code you load the CUDA module

	module load autoload cuda

and then compile the code specifying that you want to use CUDA (-Mcuda) and that you want to link the cuBLAS libraries (-Mcudalib=cublas)

~~~~~bash
nvfortran -o code_gpu.x code_gpu.f90 -Mcuda -Mcudalib=cublas  
~~~~~

------------------------------------------------------------------------

1. Take a look inside the CPU and GPU code, to have an idea of the CUDA Fortran directives.

2. Launch the two executables with varying the SIZE (substitute SIZE with an integer) of the matrices and compare the elapsed time

~~~~~bash
	./code_cpu.x SIZE

	./code_gpu.x SIZE
~~~~~

Compare the "Product times" with the theoretical peak performance reported for Marconi100:

* 0.8 TFlops per node (32 cores) and,
* 7.8 TFlops per GPU

------------------------------------------------------------------------

Unfortunately in Quantum ESPRESSO things are a bit more complicated than this, because often the matrices are initialized on the CPU and then need to be 
moved to the GPU in order to perform the computations. Sometimes also the result of the computation needs to be moved back to the CPU memory. 

This operations are usually referred to as "off-loadings" or "data transfer" between host and device memories.
The source code `code_mix.f90` shows this in a very simplified manner.  

------------------------------------------------------------------------

1. Have a look at the `code_mix.f90` file and find the data transfers between host and device memories.

2. Launch `code_mix` and `code_gpu` for large matrix sizes, and compare the elapsed times. What can you say? 

```
    ./code_mix.x SIZEs
```

------------------------------------------------------------------------

NOTE:
As a reference, for a matrix size of 8192, the times should be something around:

```
code_cpu.x
  Full time:       66.449  
  Product time:    63.170  
code_gpu.x
  Full time:        0.785  
  Product time:     0.167  
code_mix.x
  Full time:        4.236  
  Product time:     0.365  
```
