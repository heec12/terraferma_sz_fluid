# TerraFERMA Subduction Zone Fluid Flow — New Composition

## Overview

This repository contains [TerraFERMA](http://terraferma.github.io) input files for modelling fluid flow in subduction zones, adapted from:

> Wilson, C. R., Spiegelman, M., van Keken, P. E., Hacker, B. R., "Fluid flow in subduction zones: The role of solid rheology and compaction pressure", *EPSL*, 2014, [doi:10.1016/j.epsl.2014.05.052](http://dx.doi.org/10.1016/j.epsl.2014.05.052)

The key modification from the original Wilson et al. (2014) model is the replacement of the bound H2O lookup table with one derived from a **new rock composition for Archean oceanic crust estimated via Bayesian Inference** (NCFMAST system). The bound H2O field was recomputed on a P–T grid and reformatted to match the original ASCII lookup table input expected by TerraFERMA (see `data/` for the converted file).

All other model physics — solid rheology, compaction pressure formulation, subduction geometry — follow Wilson et al. (2014).

---

## Repository Structure

```
terraferma_sz_fluid/
├── data/                         # TerraFERMA input files and lookup tables
│   ├── subduction_varymobility.shml   # Simulation harness file (entry point)
│   └── ...                            # Other .tfml input files and data
├── tferma_utils/                 # Cloned at runtime (see workflow below)
├── install/                      # Runtime install prefix for tferma_utils
├── logs/                         # SLURM job logs
└── run_tf_sz_new_comp.slurm      # Job submission script (PACE/Inferno cluster)
```


---

## Running the Simulation

The simulation is designed to run on the cluster inside the official TerraFERMA [Apptainer](https://apptainer.org/) container.


**Used cluster settings** :

| Parameter | Value |
|---|---|
| Nodes / cores | 1 node, 8 tasks |
| Memory | 16 GB per CPU |
| Walltime | 336 h (14 days) |

### Running locally (with TerraFERMA installed natively)

If TerraFERMA and `tferma_utils` are already installed in your environment:

```bash
cd data/
tfsimulationharness --run subduction_varymobility.shml
```

> **Note:** simulations are configured to run on 8 MPI processes. Adjust `--ntasks-per-node` in the SLURM script or your local `mpirun` call as needed.

---

## Dependencies

| Dependency | Version | Notes |
|---|---|---|
| TerraFERMA | dev (`focal` image) | Via `ghcr.io/terraferma/dev:focal` |
| FEniCS | 2019.1.0 | Bundled in the container |
| `tferma_utils` | `fenics-2019.1.0` branch | Cloned from Bitbucket at runtime |
| Apptainer | any | Required on the HPC cluster |
| Python | 3.x | `scipy`, `numpy`, `pandas` for pre-processing |
