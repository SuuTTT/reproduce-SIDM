# Project Context Hub: SIDM (Structural Information Dynamic Modeling)

This document summarizes the research results, code relationships, and reproduction guides for three Reinforcement Learning (RL) projects based on the **Structural Information Principle** within the current workspace.

## 1. Project Overview and Relationships
The workspace contains three core research directions aimed at optimizing RL using Structural Entropy minimization:

| Project | Full Name / Direction | Core Goal | Based On |
| :--- | :--- | :--- | :--- |
| **SIRD** | Role Discovery | Multi-agent Role Discovery | [PyMARL](https://github.com/oxwhirl/pymarl) |
| **SISA** | State Abstraction | Hierarchical State Abstraction | [CURL](https://github.com/MishaLaskin/curl) / [RAD](https://github.com/MishaLaskin/rad) |
| **SISL** | Skill Learning | Hierarchical Skill Learning | [HUCC](https://github.com/facebookresearch/hucc) / [Bisk](https://github.com/facebookresearch/bipedal-skills) |

### Code Consistency Check
- **SIRD vs SR-MARL**: Core `src/` code is completely identical. The `SR-MARL` folder contains additional experimental datasets (`result/`) and more complete documentation.
- **SISA (Internal vs External)**: Internal `SIDM/SISA` and external `SISA/` source codes are completely identical, with the external version containing `.git` repository info.

## 2. Core Technical Points
The commonality among these three projects lies in injecting the **Structural Entropy algorithm** into traditional RL workflows:
- **SIRD**: Roles are assigned in `sr_controller.py` by calculating structural associations between agents.
- **SISA**: Spatial abstraction of high-dimensional states is performed in `sisa.py` using Encoding Trees.
- **SISL**: Basic skills are discovered in `SEP.py` via graph decomposition of action trajectories.

## 3. Reproduction Tracking and Issues (January 2026)

### SIRD (Multi-Agent Role Discovery)
- **Status**: Environment Ready. Training started.
- **Root Directory**: `SIDM/SIRD/`
- **Issues Encountered & "Pain Relief"**:
    1. **Dependency Gaps**: Missing `sacred`, `pysc2`, `smac`, `tensorboardX`, and `scikit-learn`.
    2. **Python 3.12 Compatibility**:
        - `PyYAML`: Added `Loader=yaml.FullLoader` to `yaml.load` in `src/main.py`.
        - `collections`: Replaced `collections.Mapping` with `collections.abc.Mapping`.
    3. **Protobuf Conflict**: `tensorboard_logger` failed with `TypeError: Descriptors cannot be created directly`. Resolved by downgrading `protobuf` to `3.20.0`.
    4. **StarCraft II Installation**: 
        - The `install_sc2.sh` script is fragile. Initial manual extraction was incomplete, leading to `ConnectError` and missing `.build.info`. 
        - **Final Resolution**: Re-extracted using `unzip -o -P iagreetotheeula SC2.4.10.zip` to ensure all 5GB+ of binaries and data files (especially `SC2Data/data/data.0xx`) were correctly placed.
    5. **Map Pathing**: PySC2/SMAC looks for maps in `Maps/SMAC_Maps/`. We had to manually move `.SC2Map` files into this subdirectory to avoid `ValueError: Map not found`.
    6. **Library Linking**: Added `$SC2PATH/Libs` to `LD_LIBRARY_PATH` to ensure the SC2 binary can find its internal OpenGL/EGL libraries.
    7. **NumPy 1.24 Compatibility**: Fixed `AttributeError: module 'numpy' has no attribute 'float'` by replacing `np.float` with `float` in `rode_controller.py` and `sr_controller.py`. This issue previously caused crashes at the role discovery phase (~40k-50k steps).

- **Solutions Implemented**:
    1. **Package Installation**: Built a custom installation sequence for Python 3.12.
    2. **Robust SC2 Setup**: Verified 24k+ files after unzip and manually managed the `Maps` hierarchy.
    3. **Background Execution**: Configured `nohup` with absolute Python paths to bypass environment variations.

### SISA / SISL
- **Status**: Partially Configured.
- **Progress**: 
    - Installed `dmc2gym`, `imageio`, and other core dependencies in the venv.
    - Verified `train.py` entry point for SISA.
    - SISL requires MuJoCo 2.1 which is pending platform verification.

## 4. Benchmark Automation
A consolidated benchmark script is available for individual or sequential project runs.

### SIRD All-Maps Batch Run
To run SIRD on all 23 SMAC maps sequentially:
```bash
nohup ./run_all_sird_maps.sh > all_maps_batch.log 2>&1 &
```
- **Status**: Currently running.
- **Maps**: 10m_vs_11m, 1c3s5z, 25m, 27m_vs_30m, 2c_vs_64zg, 2m_vs_1z, 2s3z, 2s_vs_1sc, 3m, 3s5z, 3s5z_vs_3s6z, 3s_vs_3z, 3s_vs_4z, 3s_vs_5z, 5m_vs_6m, 6h_vs_8z, 8m, 8m_vs_9m, MMM, MMM2, bane_vs_bane, corridor, so_many_baneling.

## 🚀 Execution Guide (Final)
To run the primary SIRD experiment:
```bash
export SC2PATH="/workspace/RL/SIDM/SIRD/3rdparty/StarCraftII"
export LD_LIBRARY_PATH="$SC2PATH/Libs:$LD_LIBRARY_PATH"
cd /workspace/RL/SIDM/SIRD/src
/venv/main/bin/python main.py --config=rode --env-config=sc2 with env_args.map_name=6h_vs_8z t_max=5050000
```
Check progress in `results/sacred/10/run.json` or via TensorBoard in `results/tb_logs/`.

