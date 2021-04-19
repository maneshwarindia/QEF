# Running with GPUs

To run the accelerated version you are supposed to couple _each MPI with a single GPU_. 
Therefore this time your jobscript is setup to request two MPI processes and 2 GPUs with your submission script.

1. **Analyze the difference with the previous jobscript** and submit the same input without any parallel optimization.
Once the simulation is complete, check the output file.

At the beginning of the output file you will spot

     GPU acceleration is ACTIVE.

Moreover, this run should be much faster than any of the previous CPU run.

2. Now try to **exploit the entire CPU with OpenMP**.

Set the environment variable

    export OMP_NUM_THREADS=X

with X=2,4,8. You'll notice a small improvement and, eventually a saturation.
Once again, OpenMP is effective only for large simulation, but in this case it is used to take advantage
of idle CPU cores as much as possible.

## Pool parallelism

You can improve the previous result with pool parallelism. This time you will be limited by the total number of MPI processes, namely 2.

**Modify the jobscript setting `-npool 2`** and check how the time to solution changes.

## Oversubscription

For small inputs, one can possibly obtain some additional performance by oversubscribing the GPU.

Try to increase the number of MPI processes used to run this job by changing the jobscript as shown below:

    #!/bin/bash
    #SBATCH --ntasks-per-node=4     # number of tasks per node
    #SBATCH --ntasks-per-socket=4   # number of tasks per socket
    #SBATCH --cpus-per-task=4      # number of HW threads per task (equal to OMP_NUM_THREADS*4)
    #SBATCH --gres=gpu:2            # this refers to the number of requested gpus per node, and can vary between 1 and 4
    #SBATCH --mem=230000MB
    #SBATCH --time 00:10:00         # format: HH:MM:SS
    #SBATCH -A cin_QEdevel1_4 
    #SBATCH -p m100_usr_prod 
    #SBATCH -J qeschool
    
    module load    hpc-sdk/2020--binary    spectrum_mpi/10.3.1--binary   fftw/3.3.8--spectrum_mpi--10.3.1--binary  
    
    export QE_ROOT=../example1.setup/qe-gpu/
    
    export PW=$QE_ROOT/bin/pw.x
    
    export OMP_NUM_THREADS=1
    
    mpirun  ${PW} -npool 4 -ndiag 1 -inp pw.CuO.scf.in | oversubscription


## Compare with theoretical performance

The ratio between the peak performance of the GPU and the CPU is about a factor 10. 
**Evaluate the ratio between the best time to solution of your CPU and GPU tests.**
Do your results reproduce the ideal ratio? Why not?


