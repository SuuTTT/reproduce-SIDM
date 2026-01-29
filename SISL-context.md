# SISL Project Context

## Environment
- **OS**: Linux (tested on Ubuntu)
- **Python**: 3.9 (venv: `/venv/sisl`)
- **MuJoCo**: 2.1.0 (Manual setup required, binaries in `/root/.mujoco/mujoco210`)

## System Libraries (Critical)
```bash
apt-get update
apt-get install -y libosmesa6-dev patchelf openmpi-bin libglew-dev
```

## Setup & Dependencies
1. **MuJoCo 2.1.0 Installation**:
   Download and extract `mujoco210` to `~/.mujoco/mujoco210`. Add to `LD_LIBRARY_PATH`:
   `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/root/.mujoco/mujoco210/bin`
2. **Python Environment**:
   ```bash
   python3.9 -m venv /venv/sisl
   source /venv/sisl/bin/activate
   pip install --upgrade pip
   # Note: pinned versions are critical for numba/numpy compatibility
   pip install torch==2.0.1+cpu -f https://download.pytorch.org/whl/torch_stable.html
   pip install numpy==1.21.5 numba==0.55.1 scipy==1.10.1 faiss-cpu==1.7.4 mpi4py
   pip install gym==0.23.1 mujoco-py==2.1.2.14 wandb tqdm pyyaml perlin-noise 
   ```

## Critical Code Patches (Route to Success)

1.  **Gym GoalEnv Patch**:
    `gym==0.23.1` removed `GoalEnv`. Added `class GoalEnv(gym.Env): pass` to [SISL/rl/envs/robot_env.py](SISL/rl/envs/robot_env.py) and injected it: `gym.GoalEnv = GoalEnv`.
2.  **Robotics Mocking (Library Replacement)**:
    Created [SISL/rl/envs/fetch_env_mock.py](SISL/rl/envs/fetch_env_mock.py) to replace the broken `gym-robotics` package. This provides unified observation vectors and stable rendering.
3.  **Deadlock & Hang Prevention**:
    - **MPI/Wandb Mocking**: Patched [SISL/train_sisl_agent.py](SISL/train_sisl_agent.py) to mock `mpi4py` and `wandb`, enabling single-threaded execution and avoiding remote authentication hangs.
    - **Stacking Reset Fix**: Patched [SISL/rl/envs/fetch_stack_env.py](SISL/rl/envs/fetch_stack_env.py) to add iteration limits (100) and relax placement thresholds in `reset()` to prevent infinite loops.
4.  **Stability & Reliability**:
    - **BatchNorm Guard**: Forced `skill_vae.eval()` during agent interaction in [SISL/train_sisl_agent.py](SISL/train_sisl_agent.py) to prevent `ValueError` when running single-step updates.
    - **PyTorch 2.6+ Pickle Fix**: Added `weights_only=False` to `torch.load` calls to support legacy class definitions.
5.  **Data Standardisation**:
    Updated [SISL/data/collect_demos.py](SISL/data/collect_demos.py) to handle task-specific observation slicing (e.g., 28 dims for Pyramid, 43 for Hook).

## Workflow: Step-by-Step Run

### 1. Data Collection (Multi-Task)
```bash
cd /workspace/SIDM/SISL/data
# Standardized datasets for all 5 tasks (50 trajs each)
./run_full_exp.sh  # Or run collect_demos.py individually for specific tasks
```
Datasets are stored in `SISL/dataset/fetch_[task]/demos.npy`.

### 2. Full Experiment Orchestration
The master script [SISL/run_full_exp.sh](SISL/run_full_exp.sh) handles sequential training for all tasks:
```bash
cd /workspace/SIDM/SISL
./run_full_exp.sh > full_exp.log 2>&1 &
tail -f full_exp.log
```

## Current Status
- **Environment**: 100% stabilized via unified mock and single-threading.
- **Data Collection**: Completed for Block, Pyramid, Push, CleanUp, and Hook tasks (50 trajs/each).
- **Execution**: Full multi-task experiment suite is currently running.
- **Verification**: Block task validated; Pyramid and Hook tasks undergoing skill module training.
