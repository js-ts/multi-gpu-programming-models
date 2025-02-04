# Multi GPU Programming Models
This project implements the well known multi GPU Jacobi solver with different multi GPU Programming Models:
* `single_threaded_copy`           Single Threaded using cudaMemcpy for inter GPU communication
* `multi_threaded_copy`            Multi Threaded with OpenMP using cudaMemcpy for inter GPU communication
* `multi_threaded_copy_overlap`   Multi Threaded with OpenMP using cudaMemcpy for itner GPU communication with overlapping communication
* `multi_threaded_p2p`             Multi Threaded with OpenMP using GPUDirect P2P mappings for inter GPU communication
* `multi_threaded_p2p_opt`         Multi Threaded with OpenMP using GPUDirect P2P mappings for inter GPU communication with delayed norm execution
* `multi_threaded_um`              Multi Threaded with OpenMP relying on transparent peer mappings with Unified Memory for inter GPU communication
* `mpi`                            Multi Process with MPI using CUDA-aware MPI for inter GPU communication
* `mpi_overlap`                   Multi Process with MPI using CUDA-aware MPI for inter GPU communication with overlapping communication
* `nccl`                           Multi Process with MPI and NCCL using NCCL for inter GPU communication
* `nccl_overlap`                  Multi Process with MPI and NCCL using NCCL for inter GPU communication with overlapping communication
* `nvshmem`                        Multi Process with MPI and NVSHMEM using NVSHMEM for inter GPU communication. Other approach, `nvshmem_opt`, might be better for portable performance.
* `nvshmem_opt`                    Multi Process with MPI and NVSHMEM using NVSHMEM for inter GPU communication with NVSHMEM extension API

Each variant is a stand alone Makefile project and most variants have been discussed in various GTC Talks, e.g.:
* `single_threaded_copy`, `multi_threaded_copy`, `multi_threaded_copy_overlap`, `multi_threaded_p2p`, `multi_threaded_p2p_opt`, `mpi`, `mpi_overlap` and `nvshmem` on DGX-1V at GTC Europe 2017 in [23031 - Multi GPU Programming Models](http://on-demand-gtc.gputechconf.com/gtc-quicklink/5fwZQzZ)
* `single_threaded_copy`, `multi_threaded_copy`, `multi_threaded_copy_overlap`, `multi_threaded_p2p`, `multi_threaded_p2p_opt`, `mpi`, `mpi_overlap` and `nvshmem` on DGX-2 at GTC 2019 in [S9139 - Multi GPU Programming Models](http://on-demand-gtc.gputechconf.com/gtc-quicklink/4sAYj)

# Requirements
* CUDA: verison 11.0 (9.2 if build with `DISABLE_CUB=1`) or later is required by all variants.
* OpenMP capable compiler: Required by the Multi Threaded variants. The examples have been developed and tested with gcc.
* CUDA-aware MPI: Required by the MPI and NVSHMEM variants. The examples have been developed and tested with OpenMPI.
* NVSHMEM (version 0.4.1 or later): Required by the NVSHMEM variant.
* NCCL (version 2.8 or later): Required by the NCCL variant

# Building 
Each variant come with a Makefile and can be build by simply issuing make, e.g. 
```sh
multi-gpu-programming-models$ cd multi_threaded_copy
multi_threaded_copy$ make
nvcc -DHAVE_CUB -Xcompiler -fopenmp -lineinfo -DUSE_NVTX -lnvToolsExt -gencode arch=compute_70,code=sm_70 -gencode arch=compute_80,code=sm_80 -gencode arch=compute_80,code=compute_80 -std=c++14 jacobi.cu -o jacobi
multi_threaded_copy$ ls jacobi
jacobi
```

# Run instructions
All variant have the following command line options
* `-niter`: How many iterations to carry out (default 1000)
* `-nccheck`: How often to check for convergence (default 1)
* `-nx`: Size of the domain in x direction (default 16384)
* `-ny`: Size of the domain in y direction (default 16384)
* `-csv`: Print performance results as -csv

The provided script `bench.sh` contains some examples executing all the benchmarks presented in the GTC 2019 Talk Multi GPU Programming Models.

# Developers guide
The code applies the style guide implemented in [`.clang-format`](.clang-format) file. [`clang-format`](https://clang.llvm.org/docs/ClangFormat.html) version 7 or later should be used to format the code prior to submitting it. E.g. with
```sh
multi-gpu-programming-models$ cd multi_threaded_copy
multi_threaded_copy$ clang-format -style=file -i jacobi.cu
```
