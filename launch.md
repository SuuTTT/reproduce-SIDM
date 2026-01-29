# Reproduce Guide for SIDM Projects

This guide provides step-by-step instructions to set up the environments and run the experiments for SIRD, SISA, and SISL.

## 1. Environment Setup

### Common Dependencies (Colab / Linux)
If you are running on Google Colab or a fresh Linux environment, ensure you have the necessary system libraries for MuJoCo and StarCraft II.

```bash
# System dependencies for MuJoCo and OpenGL
sudo apt-get update && sudo apt-get install -y \
    libosmesa6-dev libgl1-mesa-glx libglfw3 libglew-dev \
    unzip wget rsync

# Python base dependencies (Fixing Colab 3.12 compatibility)
pip install "numpy<2.0.0" "PyYAML>=6.0"
```

### SIRD (Multi-Agent Role Discovery)
SIRD requires StarCraft II (v2.4.10) and SMAC maps.

```bash
# Install StarCraft II and SMAC maps
cd /workspace/RL/SIDM/SIRD
# Note: Ensure EXP_DIR is set or it defaults to ~
export EXP_DIR=/workspace/RL/SIDM/SIRD
bash install_sc2.sh

# Set SC2PATH
export SC2PATH=/workspace/RL/SIDM/SIRD/3rdparty/StarCraftII
```

### SISA (State Abstraction)
SISA depends on DMControl and MuJoCo.

```bash
cd "/workspace/RL/SIDM/SISA/source code/dmcontrol"
pip install -r requirements.txt
```

### SISL (Skill Learning)
SISL uses Hydra and the \`bipedal-skills\` benchmark.

```bash
cd /workspace/RL/SIDM/SISL
pip install -e .
```

## 2. Running Experiments

### SIRD
To run SIRD (with Structural Information) vs RODE (Original baseline):

```bash
cd /workspace/RL/SIDM/SIRD

# 1. Set environment paths
export SC2PATH=/workspace/RL/SIDM/SIRD/3rdparty/StarCraftII
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SC2PATH/Libs

# 2. Launch SIRD (Structural Information-based Role Discovery)
# Note: uses absolute venv path and specific map config
nohup /venv/main/bin/python3 src/main.py --config=sr_marl --env-config=sc2 with env_args.map_name=2c_vs_64zg t_max=5050000 > sird_run.log 2>&1 &

# 3. Monitor progress
tail -f sird_run.log
```

### SISA
To run SISA on DMControl tasks:

```bash
cd "/workspace/RL/SIDM/SISA/source code/dmcontrol"
python train.py domain_name=cartpole task_name=swingup seed=1
```

### SISL
To train skill modules and then the RL agent:

```bash
cd /workspace/RL/SIDM/SISL
# 1. Train Skill VAE
python train_skill_modules.py --dataset_name hook --config_file hook.yaml

# 2. Train RL Agent with Skills
python train_sisl_agent.py --env_name complex_hook --skill_model_path ./results/saved_skill_models/hook/skill_vae.pth
```
