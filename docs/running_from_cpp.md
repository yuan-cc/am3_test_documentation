(sec_run_with_c++)=
# Running from C++

Naturally written in C++, the code can also be run directly through C++ with a Makefile.
As the python binding is a direct mapping of functions, the functions/parameters in the main function are similar to the one in python (see python scripts). However, in contrast to simply importing AM3 in Python with the command ```import pybind_AM3```, when working with C++, it is necessary to include all AM3 header files in the C++ manuscript and utilize a Makefile to specify the location of the AM3 library. Typically, we employ a C++ manuscript for executing AM3 in order to test new features or modifications in the AM3 source code. For users who do not intend to customize AM3, we recommend utilizing the Python interface.

Nonetheless we here give a simple example.

One can run AM3 with C++ by creating a C++ script, for example ```TestAM3withCpp.cc```, and a ```Makefile_CPP``` in the Am3 root directry. 

In the C++ script, all AM3 header file should be included. The class subjects should be declared according to C++ syntax. Here is one simple example

```cpp
#include "AM3/AM3Arrays.h"
#include "AM3/HadronicSynchrotron.h"
#include "AM3/PairProduction.h"    
#include "AM3/Acceleration.h"
#include "AM3/IO.h"
#include "AM3/PhysicsHandler.h"
#include "AM3/Synchrotron.h"
#include "AM3/BetheHeitler.h"        I
#include "AM3/njection.h"
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

int main{

  return 0;
}
```
