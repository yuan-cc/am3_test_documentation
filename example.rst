A simple pyAM3 example:
---------------------------
To run AM3 we use a Python script and we use a YAML file to store the parameters
for the simulation. Let's first create ``testPyAM3.yaml``:

.. code-block:: yaml

    name: testPyAM3

    SimulationManager:
        solverAlgorithm: 1
        estimateMaxEnergies: 0
        Processes:
            hadronic: 0
            inj: 1
            es: 0
            ac: 0
            ad: 0
            sy: 1
            syn_cooling: 1
            hadronic_sy: 0
            psy: 0
            psy_cooling: 0
            sy_mu: 0
            sy_pi: 0
            ic: 1
            ic_cooling: 1
            hadronic_ic: 0
            pic: 0
            ic_mu: 0
            ic_pi: 0
            pp: 0
            pair_feedback: 0
            cl: 0
            bh: 0
            bh_cooling: 0
            pg: 0
            pg_cooling: 0
            extern_pi: 0
            sec_decay: 0

    RunParams:
        # general
        R_co: 1e16 # cm
        #V_co:  # cm^3
        B_co: 1e-2 # Gauss
        #
        # time scales
        #t_dyn_co:  # s
        #FRACt:
        #t_adi_co:  # s
        #FRACs:
        #
        # injection
        P_jet: 1e50 # erg/s
        #FRACe:
        # in = 1 or 2 (monochromatic or power law)
        e_inj_index: 2.1
        #e_inj_min:
        #e_inj_max:
        # in = 3 (broken power law)
        #e_inj_idx_L:
        #e_inj_idx_H:
        #e_inj_m:
        #e_inj_b:
        #e_inj_x:
        #FRACp:
        #p_inj_index:
        #p_inj_min:
        #
        # acceleration
        #FRACa:
        #
        # Numerical
        #REG:
        #vD:

    AM3_Arrays:
        #file: abc/testfile.h5  # the h5 file to be used to store the number densities
        track:
            # default is tracking no particle
            LepE: 1  # electrons
            LepP: 0  # positrons
            LepG: 1  # Photons
            LepG1: 0  # ?
            HadP: 0  # protons
            HadN: 0  # neutrons
            MmL: 0  #
            MmR: 0  #
            MpR: 0  # 
            MpL: 0  # 
            NUe: 0  # electron neutrino
            NUeA: 0  # anti electron neutrino
            NUm: 0  # muon neutrino
            NUmA: 0  # anti muon neutrino
            Pi0: 0  # pi zero
            Pim: 0  # pi minus
            Pip: 0  # pi plus

Here we gave the run a name, chose the new solver algorithm, turned off the
estimation of the maxium Lorentz factors, configured some processes and finally
changed a few parameters of RunParams. 

An example program would then look like this:

.. code-block:: python

    from pyAM3 import pyAM3

    yamlfile = "example.yaml"
    pam = pyAM3.fromYAML(yamlfile)
    
    # change RunParams, turn on/off processes, change tracked species
    emin, emax = pam.LorentzFactor2Index(np.array([1e2, 1e4]))
    pam.update(e_inj_min=emin, e_inj_max=emax, B_co=1.5, ac=0, LepP=1)

    # initalize the kernels
    pam.prepareRun()

    # change the inital values of the particle distributions
    pam.dat.set_LepE(...)

    nSteps = ...
    
    for i in range(nSteps):
		# evolve simulation by one step
		pam.evolveStep()

    # if no file is specified in example.yaml, we can save the evolution:
    pam.dat.evolutionToFile("someName.h5")







Typical usage of pybind_core:
=============================


.. code-block:: 

	Typical usage example:

	Rblob = .. # some value for the radius
	rp = RunParams(Rblob)

	# define all the other run parameters
	...

	# initalize container for arrays of number densities
	dat = AM3_Arrays()

	# initialize class to handle the calculation of physical processes
	ph = PhysicsHandler(rp, dat)

	# initialize the main class
	sim = SimulationManager()

	# turn on/off processes
	sim.process_xy = 1

	# initialize synchrotron module of C++ code and update magnetic field
	sim.Update_B(rp.b_Gauss)

	# initialize the kernels for each included process
	initializeKernels(sim, ph)

	# set all particles' number densities to zero
	sim.Clear_Particle() 

	for i in range(nSteps):
		# evolve simulation by one step
		sim.EVO()