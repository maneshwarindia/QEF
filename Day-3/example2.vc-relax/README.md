# PURPOSE OF THE EXERCISE:
## How to perform a variable-cell structural optimization (calculation = "vc-relax").
------------------------------------------------------

There are two examples of variable cell relaxation: a hcp Zn-bulk
structure and molecular crystal of urea.


### Example-1: hcp Zn-bulk
--------------------------
  
Steps to perform:

1. Read the pw.Zn.vc-relax.in input file and try to understand
   it. Notice the new &IONS and &CELL namelists. Beware that "IONS" is
   a jargon in pseudo-potential context for atoms (can you figure out
   why?)

   
2. To run the example, execute:

       pw.x -in pw.Zn.vc-relax.in > pw.Zn.vc-relax.out &


3. When calculation finishes, analyze the output: it consists of
   several SCF steps, followed by calculation of forces and stress
   tensor, and generation of new cell parameters and atomic positions.


4. To visualize the evolution of the structure during structural
   optimization, execute:

       xcrysden --pwo pw.Zn.vc-relax.out

5. Instead of vc-relax, you can run the full 2D scan over the two
   lattice parameters with

       pwtk Zn-scan.pwtk

   This takes somewhat longer as the self-consistent DFT cycle is executed
   over several pairs of *a*, *c*.

The resulting potential-energy-surface can be visualized as:

       gnuplot plot2D.gp


### Example-2: molecular crystal of urea
----------------------------------------

Example-2 is analogous to example-1, just the structure is different.
**Beware** that it is **computationally heavier** than the example-1. To run
the example, execute:

       pw.x -in pw.urea.vc-relax.in > pw.urea.vc-relax.out &


### How to run example-2 (molecular crystal of urea) on the HPC cluster
----------------------------------------

To submit the `pw.urea.vc-relax.in` calculation to the HPC cluster, use:

      remote_mpirun pw.x -in pw.urea.vc-relax.in

**Beware #1:** in this case you must use **-in** (or **-inp**) instead of
"<". Notice also that there is no redirection to standard-output
(i.e. the "**> pw.urea.vc-relax.out**" is not specified).

**Beware #2:** note that you will need to download the calculated
output file `pw.urea.vc-relax.out`. You can do that via:

       rsync_from_hpc '*.out'

But wait some time before doing that; give the remote computer some
time to make the calculation.
