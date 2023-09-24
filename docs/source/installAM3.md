# Installing pyAM3


## Prerequisites:

1. python3
    - numpy
    - pybind11
    - ruamel.yaml (for YAML files)
    - h5py (for hdf5 files)

2. g++
    - gsl library for complicated functions in C++: :code:`sudo apt install libgsl-dev`

3. make


## Making AM3:

1. Check if the paths in the AM3 Makefile are correct. I used the following:

.. code-block:: makefile

    PYTHON_BASE = $(shell python -c "import sys; print(sys.base_prefix)")
    PATH_TO_PYTHONLIB = $(PYTHON_BASE)/lib
    PYTHONROOT = $(PYTHON_BASE)/include/python3.7m
    
2. simply use :code:`make` to compile and link AM3. This will create the library
    in your AM3 folder: :code:`libpython/lib/pybind_core.so`


## Importing pyAM3 to Python:

It is important to add two paths to your :code:`PYTHONPATH` or
append it at the beginning of your program:

- path to ``AM3/xy/libpython/lib``
- path to ``AM3/xy/libpython/pyAM3``

with ``xy`` being either ``trunk`` or your branch ``branches/XY_branch``
