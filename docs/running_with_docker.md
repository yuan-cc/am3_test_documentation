(sec_run_with_docker)=
# Running with Docker

## Prerequisites

- Docker
- Python 3.x
- Pip

## Installation

You can install the code via Docker using different files based on your OS:

### Linux

```bash
$ sudo ./linux/install_linux.sh
```

### Mac (Intel)

```bash
$ sudo ./max_intel/install_mac.sh
```

### Mac (AMR - M1 / M2)

```bash
$ sudo ./mac_M1/install_mac_M1.sh
```

Make sure to use sudo and give rights to use these executables.

## Running the code

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

### GUI Panels

The GUI has several panels:

- Parameters Input: Contains sliders for adjusting parameters, and a "Load from File" button to load a file with the same format as the passing argument seen before. Click "Launch Shell Script" to run AM3 with the selected parameters.

- Parameters: Displays the electron/proton distribution, external fields (if any), and geometry of the problem. Click "Generate Params" to update the display.