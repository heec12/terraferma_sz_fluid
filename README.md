# TerraFERMA Subduction Zone Fluid Flow — New Composition

## Overview

This repository contains [TerraFERMA](http://terraferma.github.io) input files for modelling fluid flow in subduction zones, adapted from:

> Wilson, C. R., Spiegelman, M., van Keken, P. E., Hacker, B. R., "Fluid flow in subduction zones: The role of solid rheology and compaction pressure", *EPSL*, 2014, [doi:10.1016/j.epsl.2014.05.052](http://dx.doi.org/10.1016/j.epsl.2014.05.052)

The key modification from the original Wilson et al. (2014) model is the replacement of the bound H2O lookup table with one derived from a **new rock composition for Archean oceanic crust estimated via Bayesian Inference** (NCFMAST system). The bound H2O field was recomputed on a P–T grid and reformatted to match the original ASCII lookup table input expected by TerraFERMA (see `data/` for the converted file).

All other model physics — solid rheology, compaction pressure formulation, subduction geometry — follow Wilson et al. (2014).



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

## Phase diagram lookup tables

### Filename convention
 
Files follow the pattern `7oxides_strict_kom_{structure}_{layer}_mean_{H2O}_PT_grid_P_GPa.csv`, where:
 
- `{structure}`: crustal structure assumption
  - `H` — **homogeneous** (upper and lower crust compositions are similar)
  - `L` — **layered** (upper and lower crust compositions differ; planned)
- `{layer}`: which crustal layer estimate was used
  - `uc` — upper crust
  - `lc` — lower crust
- `{H2O}`: initial water content assumption
  - `5prctH2O` — 5 wt% initial water bound
  - `saturated` — water-saturated conditions
 
### Current files
 
| File | Structure | Layer | Initial H₂O | Status |
|---|---|---|---|---|
| `7oxides_strict_kom_H_uc_mean_saturated_PT_grid_P_GPa.csv` | Homogeneous | Upper crust | Saturated | ✓ Done |
| `7oxides_strict_kom_H_uc_mean_5prctH2O_PT_grid_P_GPa.csv` | Homogeneous | Upper crust | 5 wt% | ✓ Done |
| `7oxides_strict_kom_H_lc_mean_saturated_PT_grid_P_GPa.csv` | Homogeneous | Lower crust | Saturated | ✓ Done |
| `7oxides_strict_kom_H_lc_mean_5prctH2O_PT_grid_P_GPa.csv` | Homogeneous | Lpper crust | 5 wt% | ✓ Done |
| `7oxides_strict_kom_L_*_PT_grid_P_GPa.csv` | Layered | — | — | Planned |
| `vanKekenWorldCalculator21Sep09_dikeH2O.csv` | — | Upper crust | — | Original Wilson et al. (2014) reference |
| `vanKekenWorldCalculator21Sep09_GabbroH2O.csv` | — | Lower crust | — | Original Wilson et al. (2014) reference |
| `vanKekenWorldCalculator21Sep09_PeridotiteH2O.csv` | — | Mantle | — | Original Wilson et al. (2014) reference |
 
The P–T grid CSVs are output directly from the phase diagram calculations by Perple_X.


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


## Dependencies

| Dependency | Version | Notes |
|---|---|---|
| TerraFERMA | dev (`focal` image) | Via `ghcr.io/terraferma/dev:focal` |
| FEniCS | 2019.1.0 | Bundled in the container |
| `tferma_utils` | `fenics-2019.1.0` branch | Cloned from Bitbucket at runtime |
| Apptainer | any | Required on the HPC cluster |
| Python | 3.x | `scipy`, `numpy`, `pandas` for pre-processing |
