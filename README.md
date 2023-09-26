# Welcome to the AM3 (Astrophysical Multi-Messenger Modeling) Software!

<p align="center">
  <img src="docs/media/logo.png" />
</p>



# Overview

AM3 is a software package for simulating lepto-hadronic interactions in astrophysical environments.
It solves the time-dependent partial differential equations for the energy spectra of electrons, positrons, protons, neutrons, photons, neutrinos as well as charged secondaries (pions and muons), immersed in an isotropic magnetic field. Crucially, it accounts for the fact that photons and charged secondaries emitted in electromagnetic and hadronic interactions feed back into the interaction rates in a time-dependent manner, therefore grasping non-linear effects including electromagnetic cascades. 

Among the state-of-the-art multi-messenger simulation tools [see Cerruti et al PoS ICRC2021 979 (2021)] AM3 is the most computationally efficient, making it possible to scan vast source parameter scans and fit the observational data. It has been deployed to explain multi-wavelength observations from blazars, gamma-ray bursts and tidal disruption events, for a full list of references using AM3 see [below](#list-of-papers-based-on-am3).



In this open-source release, we are making AM3 available with all its current features. The solver consists of a C++ library that can be compiled and deployed directly. Alternatively, we provide Python users with an interface that allows you to compile a shared library exposing all of AM3's high-level functions to Python 3. This means you can run simulations with AM3 in pure Python without any significant loss of efficiency.

# Documentation and Referencing 

A detailed user guide can be found under [am3.readthedocs.io](https://am3.readthedocs.io). 

If you use AM3 for you project, please cite 

# Prerequisites

1. If using Docker:
    - Docker
    - Python 3.x
    - Pip

2. If compiling and running from Python: 
    - C++11 or above
    - gsl library for mathematical special functions in C++ (`sudo apt install libgsl-dev`)
    - eigen library for matrix operations (currently included in the repo)
    - Python 3.x
    - pybind11
    - numpy

Running directly in AM3s native language C++ is also possible, removing the requirements of \[Python 3.x, pybind11, numpy \] from the above list



# Installation \& Execution

## Using Docker

You can install the code via Docker using different files based on your OS:

* Linux

```bash
$ sudo ./linux/install_linux.sh
```

* Mac (Intel)

```bash
$ sudo ./max_intel/install_mac.sh
```

* Mac (AMR - M1 / M2)

```bash
$ sudo ./mac_M1/install_mac_M1.sh
```

Make sure to use sudo and give rights to use these executables.


### Running the code

To run the code, pass the necessary arguments to the installation commands. For example on Linux:

```bash
$ ./linux/install_linux.sh {"rblob":1.0+17,"magfield":0.05,"egammamin":1e+2,"egammabrk":1e+5,"egammamax":1e+5,"eindex":2.0"eindex_brk":3.0,"elum":1e+42,"lorentz":25,"z":0.01}
```

Replace the values in the JSON object with the desired parameters.

### Running the GUI

Warning : the GUI has only been tested on Linux. 

To run the GUI code, install the required libraries via pip :

```bash
$ pip install requirements_GUI.txt
```

Then, with sudo and giving full rights :

```bash
python3 GUI_AM3.py
```

## Manual installation

### Python interface 

**1. Making AM3** 

Simply use `make` in the root AM3 directory to compile and link AM3 for the first time using the Makefile provided. For remaking remember to `make clean` first. 

Running `make lib` compiles the core C++ library followed by the shared Python library. 
If successful, this should appear as `$PYTHON_ROOT/lib/pybind_am3.so`, where `$PYTHON_ROOT` is the root directory of this repository.

To build AM3 Python library on MacOS, use the local python3 (e.g., from `/usr/local/bin/python3`) instead of the conda/anaconda one to avoid pybind11 issues.

**2. Import the library to python**
It is important to add the absolute path of the `lib/` subdirectory to your `PYTHONPATH`.

You can then import it as a module in the beginning if your Python routine:

```
import pybind_am3 as am3
```

***3. Examples in python*** 

The directory `examples/examples_in_python` contains Jupyter notebooks each containing a full AM3 workflow, from setting up the simulation to plotting the results.


### In C++

@Cheng Chao: Would you like to add an example of your current workflow to `examples/examples_in_cpp`? We could then remove the current ones, which I think are outdated.


# List of papers based on AM3
docs/list_of_papers.md