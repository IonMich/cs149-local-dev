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
| Assignment 3 | :white_check_mark: | :grey_question: | :grey_question: |
| Assignment 4 | :white_check_mark: | :grey_question: | :grey_question: |
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

With conda, we can install CUDA via NVIDIA's official [conda channel](https://anaconda.org/nvidia/cuda). However, this installation defines environment variables such as `CXX` and `CXXFLAGS` that are also referenced in the `Makefile` of the assignment. Getting the `Makefile` to work with the conda-installed CUDA and OpenGL+Glut appears to be very tricky.

A solution is to install cuda system-wide via the [installer](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_network) provided by NVIDIA for CUDA Toolkit. For the rendering part, GLUT can be installed in Ubuntu via `apt`:

```shell

sudo apt-get install freeglut3-dev
```

Finally you might need to specify the compute capability of your GPU in the `Makefile` of the assignment. Specifically for GeForce 30 series GPUs, you need to specify `-arch=sm_86` in the `NVCCFLAGS` variable in the `Makefile`:

```makefile
NVCCFLAGS = -O3 -arch=sm_86
```

You can find the compute capability of your GPU [here](https://developer.nvidia.com/cuda-gpus).

# Assignment 4

After a bit of trial and error, the following combination of package versions appears to work on Ubuntu 22.04:

```shell
conda create -n gpt149
conda activate gpt149
conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=11.8 python=3.10 numpy=1.26 ninja tiktoken -c pytorch -c nvidia -c conda-forge
```
