# Code overview

To evolve number densities $n$ as a function of time $t$ AM3 solves the time-dependent partial differential equations of the form

$$ \partial_{t}n(E,t)=-\partial_{E} \dot{E}({E,t})n(E,t)-\alpha(E,t) n(E,t)+Q(E,t) $$

It thus accounts for cooling [ $\dot{E}(E,t)$ ], escape/sink [ $\alpha(E,t)$ ] and injection/source [ $Q(E,t)$  ].


## Code structure

The user-interface module contains five classes:

  * 'SimulationManager' is the core class managing a simulation. Objects of this class will be referred to as 'sim'.
  * 'PhysicsHandler' is a helper class to collect all physics processes. It does not have to be accessed, only initialisation is required. Objects of this class will be referred to as 'ph'.
  * 'RunParams' holds the simulation parameters, such as the source details. Objects of this class will be referred to as 'rp'.
  * 'AM3Arrays' holds the data arrays during the simulation. It does not have to be accessed, only initialisation is required. Objects of this class will be referred to as 'dat'.
  * 'IO' contains the input/output possibilities. Objects of this class will be referred to as 'io'.

Note that internally all physics processes are implemented in single modules (i.e. .cc files), which however is only relevant on developer level.

## Grids

Code-internal we differentiate between three energy grids: The lepton ($e^\pm$), photon ($\gamma$) and hadronic (all other particles) ones; all equally spaced in logarithmic space (base e). The grid settings are specified during compilation and thus cannot be changed during runtime. 
Grid parameters and access:
* *Definition of energy grids* is done in include/AM3/global_defs.h .
    Photon grid length is defined by BIN_X, photon grid origin X_I. The other grid lengths are derived: BIN_E = BIN_X + X_I, BIN_P = BIN_E - 26. Grid spacing D_X = 0.1 (in e base) is not be changed, as this would lead to erroneous results.
* *Readout of grids* is via object io of class IO:
    1. Grid parameters: `io.dlnE()` [Grid spacing in ln-space], `io.NLepE()` [number of electron grid points], `io.NLepG()` [number of photon grid points], `io.NHad()` [number of hadronic grid points], `io.NNU()` [number of neutrino grid points] 
    2. Grids in eV: `io.E_LepE_eV()` [array of electron grid], `io.E_LepG_eV()` [array of photon grid], `io.E_Had_eV()` [array of hadronic grid], `io.E_Nu_eV()` [array of neutrino grid]
    3. For conversion of energy in eV to gridpoint, users may rely on `io.EeV2i_LepE()` / `io.EeV2i_LepG()` / `io.EeV2i_Had()` / `io.EeV2i_Nu()`

In contrast to the energy grid, the time step may be adjusted dynamically and is accessible through the parameter "dt" of the class RunParams, thus `rp.dt` (in s). 

## Source parameters

AM3 pre-assumes homogeneous particle and magnetic fields.


## Particle species

The code calculates the time-dependent evolution of the following species:

|Particle                   |Symbol             |Internal name  |
| -------------             | ---------         | -----------   |
| Electrons                 | $e^-$             | LepE          |
| Positrons                 | $e^+$             | LepP          |
| Photons                   | $\gamma$          | LepG          |
| Protons                   | $p$               | HadP          |
| Neutrons                  | $n$               | HadN          |
| Left-handed muons         | $\mu^-_L$         | MmL           |
| Right-handed muons        | $\mu^-_R$         | MmR           |
| Left-handed anti-muons    | $\mu^+_L$         | MpL           |
| Right-handed anti-muons   | $\mu^+_R$         | MpR           |
| Positive pions            | $\pi^+$           | Pip           |
| Negatie pions             | $\pi^-$           | Pim           |
| Muon neutrinos            | $\nu_{\mu}$       | Num           |
| Muon anti-neutrinos       | $\bar{\nu}_\mu$   | NumA          |
| Electon neutrinos         | $\nu_e$           | Nue           |
| Election anti-neutrinos   | $\bar{\nu}_e$     | NueA          |

### Accessing the particle distributions

Access is implemented through the IO class (with corresponding object io):

* The particle species can be accessed through get/set functions: \
    The scheme for getters is 

    > io.Edn\_dE\_ + \${Internal name} + () 
    
    For example `io.Edn_dE_LepE()`. For setting with an array input_array use

    > io.set_Edn\_dE\_ + \${Internal name} + (input\_array) 

    For example `io.set_Edn_dE_LepE(input_array)`. 
    Units are 1/cm$^3$ for all.
* Collecting *all* particles of a species: \
    Neutrinos may be read out as all-flavour array through `io.Edn_dE_Nu()`, 
    the summed distributions of positrons and electrons through `io.Edn_dE_LepEP()`,
    the summed distributions of all muons through `io.Edn_dE_Mu()` and 
    the summed distributions of positive and negative pions through `io.Edn_dE_Pipm()`.
* Photons by emitting process: Photons can be separated by the physics process they originate from. \
    If they are generated by a secondary electron/positron population, the pattern is

    > io.Edn\_dE\_LepG\_ + \${Process abbreviation} + \${sy/ic/syic}

    The process abbreviations are introduced [below](#physics-processes), in addition primary (injected) electrons have the process abbreviation 'in'. 
    \${sy/ic/syic} specifies whether the photons are due to [synchrotron/inverse Compton/synchrotron and inverse Compton radiation] of the leptons.
    Thus, the command finally may read for example `io.Edn\_dE\_LepG\_bhsy()` or `io.Edn\_dE\_LepG\_insy()`. \
    Synchrotron and inverse Compton radiation from pions, muons and protons are accessed through

    > io.Edn\_dE\_LepG\_ + \${particle abbreviation} + \${sy/ic/syic}

    Here, the particle abbreviations are 'pi' for pions, 'mu' for muons and 'p' for protons, yielding for example `io.Edn\_dE\_LepG\_psy()` for proton synchrotron radiation. \ 
    Finally, Photons from $\pi^0$ decays are accessed through  `io.Edn\_dE\_LepG\_pi0()`.
* Secondary leptons by generating process: Secondary lepton pairs may be accessed in a similar pattern as photons, thus

    > io.Edn\_dE\_LepEP\_ + \${Process abbreviation}

    For example `io.Edn_dE_LepEP_pg()`. Here we again use the process abbreviations (bh/pair/pg) introduced [below](#physics-processes) and 'in' for (internally) injected leptons. 

## Physics processes

The following physics processes are included: 

|Process                        |Abbreviation   | Terms entering the differential equations                             |
|--------                       | ------------  | ------------------------------                                        |   
|Synchrotron                    | sy            | $Q_\gamma$, $\alpha_\gamma$, $\dot{E}$ for all charged particles      |
|Inverse Compton                | ic            | $Q_\gamma$, $\alpha_\gamma$, $\dot{E}$ for all charged particles      |
|$\gamma \gamma$ annihilation   | pair          | $\alpha_\gamma$, $Q_{e^{\pm}}$                                        |
|Photo-pair production          | bh            | $\dot{E}_p$, $Q_{e^{\pm}}$                                            |
|Photo-pion production          | pg            | $\alpha_{\gamma}$, $\alpha_p$, $Q_{\pi^\pm}$, $Q_\gamma$              |
|Adiabatic expansion            | ad            | $\alpha$ for all particles, $\dot{E}$ for all charged particles       |
|Escape                         | es            | $\alpha$ for all particles                                            |
|Pion decay                     | dec           | $\alpha_{\pi^\pm}$, $Q_{\mu^\pm}$, $Q_{\nu_\mu}$                      |
|Muon decay                     | dec           | $\alpha_{\mu^\pm}$, $Q_{e^\pm}$, $Q_{\nu_\mu}$, $Q_{\nu_e}$           |
|Injection                      | inj           | $Q_{e^{-}}$, $Q_p$                                                    |

**Notes**: (1) Due to their short lifetime, neutral pions are assumed to decay instantaneously. For photo-pion production we hence don't list a source term for neutral pions but instead a photon source term. (2) Injection here refers to the build-in injection. Arbitrary injection is possible by passing arrays. 

### Accessing timescales

Particle timescales (in s) can be accessed through  

> io.t\_ + \${Particle internal name} + \_ + \${Process abbreviation}

For example `io.t_LepE_sy()`. \
In addition to the processes listed above it is possible to retrieve the acceleration timescale for electrons and protons. It is defined as 

$$ t_\mathrm{acc} = \eta $$

where the acceleration efficiency $\eta$ can be set through

### Injection of arbitrary distributions

Arbitrary particle distributions may be injected by passing an input_array (in 1/cm$^3$s) to AM3, following the pattern

> io.set\_Edn\_dEdt\_ext\_ + \${Particle internal name} + (input_array)

For example `io.set_Edn_dEdt_ext_HadP(input_array)`. Please ensure before that the array size is correct, no internal checks are implemented! \
The corresponding get-functions follow the pattern 

> io.Edn\_dEdt\_ext\_ + \${Particle internal name}

For example `io.Edn_dEdt_ext_HadP()`. \

### Internal injection 

The internal injection arrays are accessed through

> io.Edn\_dEdt\_int\_ + \${Particle internal name}

For example `io.Edn_dEdt_int_HadP()`. \
