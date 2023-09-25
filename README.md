# Welcome to the AM3 (Astrophysical Multi-Messenger Modeling) Software!

![](docs/media/logo.png)


# Overview

AM3 is a software package for simulating lepto-hadronic interactions in astrophysical environments.
It solves the time-dependent partial differential equations for the energy spectra of electrons, positrons, protons, neutrons, photons, neutrinos as well as charged secondaries (pions and muons), immersed in an isotropic magnetic field. Crucially, it accounts for the fact that photons and charged secondaries emitted in electromagnetic and hadronic interactions feed back into the interaction rates in a time-dependent manner, therefore grasping non-linear effects including electromagnetic cascades. 

Among the state-of-the-art multi-messenger simulation tools [see Cerruti et al PoS ICRC2021 979 (2021)] AM3 is the most computationally efficient, making it possible to scan vast source parameter scans and fit the observational data. It has been deployed to explain multi-wavelength observations from blazars, gamma-ray bursts and tidal disruption events, for a full list of references using AM3 see [below](#list-of-papers-based-on-am3).



In this open-source release, we are making AM3 available with all its current features. The solver consists of a C++ library that can be compiled and deployed directly. Alternatively, we provide Python users with an interface that allows you to compile a shared library exposing all of AM3's high-level functions to Python 3. This means you can run simulations with AM3 in pure Python without any significant loss of efficiency.

# Prerequisites

1. If using Docker
    - @GFC: can you put in a few words on this?
    - This requirement replaces the ones below at least partially, correct?

2. If using the Python interface without Docker
    - Python 3.7 or above 
    - pybind11
    - numpy

3. If using either the Python interface or the C++ library without Docker
    - C++11 or above
    - gsl library for mathematical special functions in C++ (`sudo apt install libgsl-dev`)
    - eigen library for matrix operations (currently included in the repo)

# Documentation and Referencing 

A detailed user guide can be found under [am3.readthedocs.io](https://am3.readthedocs.io). 

If you use AM3 for you project, please cite 
# Quickstart


## Installation

### Installation using Docker

@GFC: a few words on this?

### Manual installation

**1. Making AM3**

Simply use `make` in the root AM3 directory to compile and link AM3 for the first time using the Makefile provided. For remaking remember to `make clean` first. 

Running `make lib` compiles the core C++ library followed by the shared Python library. 
If successful, this should appear as `$PYTHON_ROOT/lib/pybind_am3.so`, where `$PYTHON_ROOT` is the root directory of this repository.

To build AM3 Python library on MacOS, use the local python3 (e.g., from `/usr/local/bin/python3`) instead of the conda/anaconda one to avoid pybind11 issues.

** 2. Importing `pybind_am3` to python**
It is important to add the absolute path of the `lib/` subdirectory to your `PYTHONPATH`.

You can then import it as a module in the beginning if your Python routine:

```
import pybind_am3 as am3
```

## Examples 

### In Python

The directory `examples/examples_in_python` contains Jupyter notebooks each containing a full AM3 workflow, from setting up the simulation to plotting the results.


### In C++

@Cheng Chao: Would you like to add an example of your current workflow to `examples/examples_in_cpp`? We could then remove the current ones, which I think are outdated.


# List of papers based on AM3
