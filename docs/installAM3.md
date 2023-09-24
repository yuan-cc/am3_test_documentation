# Installing AM3

AM3 can be installed either through docker or by compiling the code yourself.

## Docker

## Compiling yourself

### Prerequisites:

1. python3
    - pybind11

2. g++
    - gsl library: `sudo apt install libgsl-dev`

3. make

### Making AM3:

1. Check if the paths in the AM3 Makefile are correct. I used the following:

    ````
    PYTHON_BASE = $(shell python -c "import sys; print(sys.base_prefix)")
    PATH_TO_PYTHONLIB = $(PYTHON_BASE)/lib
    PYTHONROOT = $(PYTHON_BASE)/include/python3.7m

    ````
    
2. Run `make` to compile and link AM3. This will create the library
    in your AM3 folder: `libpython/lib/pybind_core.so`


### Importing the library in python:

In order to use the library, add its path ``AM3/libpython/lib`` your `PYTHONPATH` or
append it at the beginning of your program.
