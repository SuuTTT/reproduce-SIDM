# SISA (Structural Information State Abstraction) - Setup & Launch Guide

## 1. Environment Setup (The "Pain-Free" Way)

### Automated Patching
If cloning the original SISA/RAD repo, run these commands inside `source code/dmcontrol/` to fix known compatibility bugs:
```bash
# 1. Fix git protocol in requirements
sed -i 's/git:\/\/github.com/https:\/\/github.com/g' requirements.txt

# 2. Add missing dependencies to requirements
echo -e "matplotlib\ntqdm\nnetworkx\npandas\nnumba" >> requirements.txt

# 3. Patch dmc2gym (Fixes NumPy 2.0+ AttributeError: np.int)
sed -i 's/np.int/int/g' dmc2gym/dmc2gym/wrappers.py
```

### Batch Installation
```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
pip install "numpy<2.0" "gym==0.25.2"
pip install -r requirements.txt
pip install -e dmc2gym/
```

### Environment Verification
Run this to ensure GPU rendering and SISA imports are working:
```bash
export MUJOCO_GL=egl
python3 -c "import torch; print('CUDA:', torch.cuda.is_available()); import dmc2gym, hcse; print('Imports: OK')"
```

## 2. Launch Documentation

### Performance Run (Headless)
To run SISA on a headless server using GPU (EGL), use:
```bash
export MUJOCO_GL=egl
export PYTHONPATH=$PWD/dmc2gym:$PYTHONPATH
python3 train.py \
    --domain_name cartpole --task_name swingup \
    --sisa --replicate \
    --num_train_steps 100000 \
    --work_dir ./tmp/sisa_run
```

### Important Arguments
- `--sisa`: Enables the Structural Information State Abstraction.
- `--replicate`: Replicates settings from the original RAD paper.
- `--sisa_pretrain_steps [N]`: Sets the number of initial steps for state abstraction pretraining (e.g., 1000).
- `--action_repeat`: Recommended values: 8 for Cartpole, 4 for Ball-in-cup/Cheetah/Walker.

## 3. Benchmarking
A benchmark comparing **SISA** vs **RAD (Baseline)** is currently active:
- **Location**: `/workspace/benchmark/`
- **Baseline Log**: `/workspace/benchmark/baseline.log`
- **SISA Log**: `/workspace/benchmark/sisa.log`

## 4. Troubleshooting & Solutions

| Category | Issue | Resolution |
| :--- | :--- | :--- |
| **Connectivity** | `git://` protocol connection timeouts. | Changed URLs to `https://` in `requirements.txt`. |
| **Versions** | `numpy 2.x` broke `gym` and various wrappers. | Downgraded to `numpy 1.26.4`. |
| **API Mismatch** | `gym 0.26+` uses a different `step()` return format. | Reverted to `gym 0.25.2` for backward compatibility. |
| **Missing Modules** | `ModuleNotFoundError` for `matplotlib`, `numba`, etc. | Manually installed required packages. |
| **Code Bug** | `AttributeError: module 'numpy' has no attribute 'int'`. | Replaced `np.int` with `int` in `dmc2gym/dmc2gym/wrappers.py`. |
| **Rendering** | Headless environment error for MuJoCo rendering. | Set `MUJOCO_GL=egl`. |

## How to Run Verified Command
```bash
cd "/workspace/SIDM/SISA/source code/dmcontrol/"
export PYTHONPATH=$PWD/dmc2gym:$PYTHONPATH
export MUJOCO_GL=egl
python3 train.py --domain_name cartpole --task_name swingup --num_train_steps 50 --eval_freq 100 --init_steps 10 --sisa_pretrain_steps 10 --work_dir /workspace/sisa_test --tag sisa_test_final --sisa --replicate --sisa_pretrain_batch_size 32
```

## 5. Full Benchmark (Active)
A comprehensive benchmark comparison across 6 tasks is currently running in the background.

### Task List
1.  **ball_in_cup-catch**
2.  **cartpole-swingup**
3.  **cheetah-run**
4.  **finger-spin**
5.  **reacher-easy**
6.  **walker-walk**

### Monitoring Commands
- **Check Manager Progress**: `tail -f /workspace/benchmark_manager.log`
- **Check Current Run Details**: `tail -f /workspace/full_benchmark/sisa_ball_in_cup_catch.log`
- **Monitor System Load (GPU)**: `nvidia-smi`

### Results Directory
Experimental data (CSVs, checkpoints) will be saved in:
`[SISA]/source code/dmcontrol/exp/`

### Bug Report (2026-01-28)
- **Error**: `ZeroDivisionError: float division by zero` in `hcse/structural_optimization.py` at line 323.
- **Context**: Occurred during SISA training on `ball_in_cup-catch` at step 4700.
- **Root Cause**: `cp_sum[nid1]` was 0.0 because the sum of conditional structural entropy values for a specific node row was zero.
- **Fixed**: Added zero-check for `cp_sum[nid1]` before division in `construct_graph`.
