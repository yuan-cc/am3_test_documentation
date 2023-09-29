(sec_run_with_c++)=
# Running from C++

Naturally written in C++, the code can also be run directly through C++ with a Makefile.
As the python binding is a direct mapping of functions, the functions/parameters in the main function are similar to the one in python (see python scripts). 

In contrast to simply importing AM3 in Python with the command ```import pybind_AM3```, when working with C++, it is necessary to include all AM3 header files in the C++ manuscript and utilize a Makefile to specify the location of the AM3 library. Typically, we employ a C++ script for executing AM3 in order to test new features or modifications in the AM3 source code. For users who do not intend to customize AM3, we recommend utilizing the Python interface.

Nonetheless we here give a simple example.

One can run AM3 with C++ by creating a C++ script, for example ```TestAM3withCpp.cc```, and a Makefile, e.g., ```Makefile_CPP```, in the AM3 root directry. 

## C++ script example

In the C++ script, all AM3 header file should be included. The class subjects should be declared according to C++ syntax. 

Here is one simple example
```cpp
#include "AM3/AM3Arrays.h"
#include "AM3/HadronicSynchrotron.h"
#include "AM3/PairProduction.h"    
#include "AM3/Acceleration.h"
#include "AM3/IO.h"
#include "AM3/PhysicsHandler.h"
#include "AM3/Synchrotron.h"
#include "AM3/BetheHeitler.h"        
#include "AM3/Injection.h"
#include "AM3/PionDecay.h"
#include "AM3/consts.h"
#include "AM3/Escape.h"
#include "AM3/InverseCompton.h"
#include "AM3/RunParams.h"
#include "AM3/global_defs.h"
#include "AM3/Expansion.h"
#include "AM3/MuonDecay.h"
#include "AM3/SimulationManager.h"
#include "AM3/math.h"
#include "AM3/HadronicInverseCompton.h"
#include "AM3/PGamma.h"
#include "AM3/Solver.h"
#include <iostream>

using namespace std;

int main()
{
  RunParams rp;
  AM3Arrays * dat = new AM3Arrays();
  PhysicsHandler ph(rp,*dat);
  SimulationManager sim(rp,ph);


  sim.process_parse_sed = 1;
  sim.process_hadronic = 0;
  sim.process_mergePositronsIntoElectrons = 0;
  
  sim.process_es = 1;
  sim.escape_type = 1;
  sim.process_es_photonOptThick = 0;
  sim.process_in = 1;
  sim.internal_inj_type = 2;
  sim.process_inext = 1;
  // expansion
  sim.process_adi = 0;
  sim.process_exp = 0;
  // syn
  sim.process_esy = 1;
  sim.process_esyrad = 1;
  sim.process_esycool = 1;
  sim.process_qsyn = 0;
  sim.process_ssa = 1;
  // ic
  sim.process_eic = 1;
  sim.process_eicrad = 1;
  sim.process_eicrad_fast = 1;  // values from Shans optimisation
  sim.eicrad_fast_n_photon_in = 2;
  sim.eicrad_fast_n_photon_out = 2;
  sim.eicrad_fast_photon_in_max = 280;
  sim.eicrad_fast_photon_out_min = 200;
  sim.process_eiccool = 1;
  sim.process_eic_photonLoss = 1;
  
  sim.profile_timing = 0;
  sim.InitKernels();
  sim.Clear_Particles();

  rp.e_inj_Emin_eV = 1e9;
  rp.e_inj_Emax_eV = 1e13;
  rp.t_esc = 1e6;
  rp.p_in = 1e3;
  rp.FRACe = 1.0;
  rp.FRACp = 0.0;
  rp.dt = 0.001 * rp.t_esc;
  sim.Update_B(0.5);

  sim.EvolveStep();

  

  return 0;
}
```
## Makefile example

One Makefile, e.g., ```Makefile_CPP```, is needed to compile the C++ script with the AM3 and link them to obtain the executable file ```TestAM3withCpp```. 

Here is an example

```make
PATH_TO_MAIN = .
PATH_TO_AM3 = .
PATH_TO_Astro = ./AstroSources

CPP = c++

CPPFLAGS2 = -fPIC -O4 -std=c++11 -lgsl -lgslcblas
# The following for debugging mode:
#CPPFLAGS = -fopenmp -O0 -g -std=c++11 

# Linking flag for Eigen
PATH_TO_EIGEN = $(PATH_TO_AM3)/libEigen

# Linking flags for compiling shared library
INCLU = -I$(PATH_TO_AM3)/include -I$(PATH_TO_EIGEN) -I$(PATH_TO_Astro)/include 

# AM3 modules
AM3_SRCS = $(wildcard $(PATH_TO_AM3)/src/*.cc)
AM3_OBJS = $(AM3_SRCS:.cc=.o)
AM3_HEADERS = $(wildcard $(PATH_TO_AM3)/include/AM3/*.h)

$(PATH_TO_AM3)/src/%.o : $(PATH_TO_AM3)/src/%.cc
	@echo "Compiling AM3 library..."
	$(CPP) $(CPPFLAGS2)  $(INCLU) -c -o $@ $<

all : $(PATH_TO_MAIN)/TestAM3withCpp

$(PATH_TO_MAIN)/TestAM3withCpp : $(Astro_OBJS)  $(PATH_TO_MAIN)/TestAM3withCpp.o $(AM3_OBJS) 
	$(CPP) $(PATH_TO_MAIN)/TestAM3withCpp.o $(AM3_OBJS) -o $(PATH_TO_MAIN)/TestAM3withCpp $(CPPFLAGS2)

$(PATH_TO_MAIN)/TestAM3withCpp.o : $(PATH_TO_MAIN)/TestAM3withCpp.cc
	$(CPP) $(CPPFLAGS2)  $(INCLU) -c $(PATH_TO_MAIN)/TestAM3withCpp.cc -o $(PATH_TO_MAIN)/TestAM3withCpp.o

.PHONY: clean
clean:
	rm -f $(PATH_TO_MAIN)/TestAM3withCpp.o $(AM3_OBJS) 

```

## Run the code

With ```TestAM3withCpp``` and ```TestAM3withCpp.cc``` in the AM3 root directory, one can compile and run the C++ code with the shell command
```
$ make -f Makefile_CPP && ./TestAM3withCpp
```
The object files can be removed by running
```
$ make clean -f Makefile_CPP
```
