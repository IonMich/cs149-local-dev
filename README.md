# cs149-local-dev
Recently Stanford published all [video recordings](https://www.youtube.com/playlist?list=PLoROMvodv4rMp7MTFr4hQsDEcX7Bx6Odp) from the Fall 2023 CS149 (Parallel Computing) course. It is an exellent resource for learning about parallel computing on CPUs and GPUs, and hardware specific programming languages like CUDA and ISPC.

It appears that the course offers allocation of AWS instances and access to Stanford's [Myth machines](https://web.stanford.edu/class/cs107a/notes/myth-machines). This repository is an attempt to provide instructions for setting up a local development environment for the course programming assignments. 

Some important notes:
- The assignments occasionally provide local installation instructions, but they are not always cross-platform. **Always check first the original assignment repositories for the most up-to-date instructions.** This repository is not affiliated with Stanford University.
- About half of Assignment 1 involves ISPC code. Check the [ISPC](https://ispc.github.io/downloads.html) website to verify that your system is compatible. It appears that while ISPC was originally developed for Intel CPUs, ISPC is now compatible with AMD and ARM-based CPUs as well, including Apple's ARM-based M chips.
- Assignments 3 and 4 involve CUDA code. You will need an NVIDIA GPU to complete these assignments.
- These instructions have been tested only on a Ubuntu 22.04 machine with an RTX 3060Ti. If you have tested the instructions on a different system, please submit a pull request to update this README.

<!-- table of testing -->
<!-- explain -->
- :white_check_mark: - Tested and working
- :yellow_circle: - Tested and partially working
- :grey_question: - Not tested
- :x: - Could not get it to work

| Assignment   | Ubuntu 22.04       | MacOS 12.3      | Windows 10      |
| ------------ | ------------------ | --------------- | --------------- |
| Assignment 1 | :white_check_mark: | :grey_question: | :grey_question: |
| Assignment 2 | :white_check_mark: | :grey_question: | :grey_question: |
| Assignment 3 | :yellow_circle:    | :grey_question: | :grey_question: |
| Assignment 4 | :grey_question:    | :grey_question: | :grey_question: |
| Assignment 5 | :grey_question:    | :grey_question: | :grey_question: |

This repository provides `conda` installation instructions for Stanford's CS149 (Parallel Computing) programming assignments. With `conda` you can isolate the particular Python environment needed for each assignment (or the course as a whole), and avoid conflicts with other projects on your system.

## Getting Started

- Verify that `gcc` and `g++` are both installed on your system and that their versions match.

   ```bash
   gcc --version
   g++ --version
   ```
   If they do not match, follow this StackOverflow [post](https://askubuntu.com/a/26502) to create the appropriate symlinks. Tested with `gcc`/`g++` version 12.3.0 on Ubuntu 22.04.
- Create a new conda environment for the course:

   ```bash
   conda create -n cs149
   conda activate cs149
   ```


## Assignment 1

Link to Official Github Repo: [Assignment 1](https://github.com/stanford-cs149/asst1)


Clone the repository and install the dependencies:

```shell

git clone git@github.com:stanford-cs149/asst1.git
cd asst1
```

**ISPC:** On Ubuntu the prefered method of installation is through [the Snap store](https://snapcraft.io/ispc). Alternatively, with the newly created `cs149` conda environment activated, install ispc from the conda-forge channel:

```shell
conda install conda-forge::ispc
```

That's it! You should now be able to build the executables for each part of the assignment by running `make` in the respective directories. 

> [!NOTE]
> In Program 6 (K-Means) you need to uncomment the portion of `main.cpp` that generates the input data. After you have generated the input data, you can comment out that portion of the code and run the program as normal.

## Assignment 2

Link to Official Github Repo: [Assignment 2](https://github.com/stanford-cs149/asst2)

No additional dependencies are required for this assignment (a compatible `g++` is all we need). 

Clone the repository and build the executables for each part of the assignment by running `make` in the respective directories. The included executable `part_a/runtasks_ref_linux` also was tested and it works.

## Assignment 3

Link to Official Github Repo: [Assignment 3](https://github.com/stanford-cs149/asst3)

Clone the repository via git as in the previous assignments. 

> [!NOTE] 
> This assignment requires a compatible NVIDIA GPU and CUDA.

> [!NOTE]  
> As mentioned in one of the provided `readme` [here](https://github.com/stanford-cs149/asst3/blob/master/cloud_readme.md#setting-up-the-vm-environment), if you are on an Ubuntu 22.04 machine, you may be able to install everything via the `install.sh` script provided in the assignment repository:
>   
>   ```shell
>   chmod +x ./install.sh
>   ./install.sh
>   ```
> However that this script is intended for the AWS VMs, so use it at your own risk.

With conda, we can install CUDA via NVIDIA's official [conda channel](https://anaconda.org/nvidia/cuda):

```shell
conda install nvidia::cuda
```

This will install the latest version of CUDA available in the conda channel. If you need a specific version of CUDA, you can specify a label, as explained in the NVIDIA conda channel.

This installation of CUDA is not automatically compatible with the provided `Makefile`s in the various parts of this assignment. You will need to modify the `Makefile` to point to the correct CUDA installation directory and change the `gcc` invocation.

Specifically, here is the modified portion of the `Makefile` that appears to work for Part 1:

```makefile

OBJDIR=objs
# CXX=g++ -m64
# CXXFLAGS=-O3 -Wall

# Building on Linux
LDFLAGS=-L/home/<user>/anaconda3/envs/cs149/lib/ -lcudart
NVCC=nvcc
NVCCFLAGS=-O3 -m64 -arch=sm_86

```
where `<user>` is your username. Here we use `-arch=sm_86` to specify the compute capability of the RTX 3060Ti. You can find the compute capability of your GPU [here](https://developer.nvidia.com/cuda-gpus).

After modifying the `Makefile`, you should be able to build the executables for part 1 of the assignment by running `make` in the corresponding directory. 

The same changes to the `Makefile` should be made for Part 2.

For the main dish, Part 3 (A Simple Circle Renderer), there is an additional dependency on [freeglut](https://freeglut.sourceforge.net/index.php#download). On Ubuntu, you can install GLUT via `apt`:

```shell

sudo apt-get install freeglut3-dev
```