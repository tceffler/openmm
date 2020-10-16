This repo is just meant as a tutorial for my group on how to set up openmm on summit.
Most of the material is taken directly from the inspiremd git repo on the same subject that can be found [here](https://github.com/inspiremd/conda-recipes-summit).
Also, examples provided are taken from the openmm git repo that can be found [here](https://github.com/openmm/openmm).

# Setting up OpenMM on Summit

## Mise en Place

First, install `miniconda`:
```bash
#This script is already included in the repo but if you want the newest version
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-ppc64le.sh
bash Miniconda3-latest-Linux-ppc64le.sh -b -p miniconda
# Initialize your ~/.bash_profile
conda init bash
```

The `ppc64le` packages have been uploaded to the [`omnia`](https://anaconda.org/omnia) and [`conda-forge`](https://anaconda.org/conda-forge) channels:
```bash
# Add conda-forge and omnia to your channel list
conda config --add channels omnia --add channels conda-forge
# Update to conda-forge versions of packages
conda update --yes --all
```
## Installing OpenMM

```bash
# Create a new environment named 'openmm'
conda create -n openmm python==3.7
# Activate it
conda activate openmm
# Install the 'openmm' 7.4.0 dev package for ppc64le into this environment
conda install --yes -c omnia-dev/label/cuda101 openmm
```

## Testing openmm

```bash
# Log into a batch node
bsub -W 2:00 -nnodes 1 -P bip198 -alloc_flags gpudefault -Is /bin/bash

# Make sure to activate conda environment
conda activate openmm

# Load the CUDA and appropriate MPI modules:
module load cuda/10.1.105 gcc/8.1.1

# Run the benchmark via jsrun requesting
# one resource set (-n 1), one MPI process (-a 1), one core (-c 1), one GPU (-g 1)
cd ~/openmm/examples
jsrun --smpiargs="none" -n 1 -a 1 -c 1 -g 1 python benchmark.py --platform=CUDA --test=pme --precision=mixed --seconds=30 --heavy-hydrogens
```
I see the following benchmarks on Summit:
```
Platform: CUDA
Precision: mixed

Test: pme (cutoff=0.9)
Step Size: 5 fs
Integrated 48894 steps in 28.5644 seconds
739.46 ns/day
```
