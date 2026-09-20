# Deep Reinforcement Learning for Active Flow Control

A reinforcement-learning environment for closed-loop active flow control (AFC),
built on [Gym-preCICE](https://github.com/gymprecice/gymprecice) coupling
OpenFOAM (CFD) with a PyTorch PPO controller via the
[preCICE](https://precice.org/) coupling library.

**Status:** working baseline case, derived from the
[Gym-preCICE tutorials](https://github.com/gymprecice/tutorials)
`jet_cylinder` example. Parallel-scaling benchmarks, a validated cylinder
baseline, and one extension (airfoil geometry or a physics-informed
component) are planned next — see the roadmap referenced below.

## What this is

The agent controls the flow rate of two synthetic jets on a cylinder immersed
in a 2D incompressible channel flow (Re ≈ 100), observing 151 pressure probes
and receiving a reward based on drag/lift coefficients. The goal is to learn
an actuation policy that reduces drag, following the setup described by
Rabault et al. (2019), *"Artificial neural networks trained through deep
reinforcement learning discover control strategies for active flow control,"*
[JFM](https://doi.org/10.1017/jfm.2019.62).

## Project structure

```
jet_cylinder/
├── environment.py                     # Gym-preCICE Adapter: JetCylinder2DEnv
├── ppo_controller.py                  # CleanRL-derived PyTorch PPO controller
└── physics-simulation-engine/
    ├── gymprecice-config.json         # adapter configuration
    ├── precice-config.xml             # preCICE coupling configuration
    └── fluid-openfoam/                # OpenFOAM case (mesh, fields, solver dicts)
```

## Requirements

- OpenFOAM (tested against v2112) with the
  [OpenFOAM-preCICE adapter](https://precice.org/adapter-openfoam-overview.html)
- [preCICE](https://precice.org/) v2.5.0
- Python 3.10+, `gymprecice`, `gymnasium`, `torch`, `scipy`, `mpi4py`

This project targets a Linux/WSL environment for the OpenFOAM/MPI toolchain.

## Running

From this directory:

```bash
python3 -u ppo_controller.py --num-envs 2 --total-timesteps 96000
```

By default this launches 2 parallel Gym environments, each backed by a
2-rank MPI OpenFOAM simulation (4 CFD ranks total). Output is written to a
`gymprecice-run` directory here unless configured otherwise. See
`ppo_controller.py --help` for all training arguments.

## Roadmap

The full phase-by-phase plan (baseline validation, parallel-scaling
benchmarks, and the airfoil vs. physics-informed extension) lives one level
up, at [`../ROADMAP.md`](../ROADMAP.md), alongside supporting reference PDFs.
That folder isn't part of this repository yet — consider it a companion
workspace document for now.

## Attribution and licensing

This repository builds on tutorial code from
[gymprecice/tutorials](https://github.com/gymprecice/tutorials) (MIT License,
Copyright (c) 2023 Mosayeb Shams, Ahmed H. Elsheikh). Please retain the
`LICENSE` file from that project when redistributing.

- `ppo_controller.py` is adapted from
  [CleanRL](https://github.com/vwxyzjn/cleanrl) (MIT License, Copyright (c)
  2019 CleanRL developers).
- The OpenFOAM mesh in `physics-simulation-engine/fluid-openfoam` is adapted
  from [DRLinFluids](https://github.com/venturi123/DRLinFluids)
  (Apache License 2.0, Copyright (c) 2022, Qiulei Wang).

If you use Gym-preCICE, please cite:

```bibtex
@misc{shams2023gymprecice,
      title={Gym-preCICE: Reinforcement learning environments for active flow control},
      author={Shams, Mosayeb and Elsheikh, Ahmed H},
      journal={SoftwareX},
      volume={23},
      pages={101446},
      year={2023},
      issn={2352-7110},
      doi={https://doi.org/10.1016/j.softx.2023.101446},
      eprint={https://arxiv.org/abs/2305.02033},
}
```
