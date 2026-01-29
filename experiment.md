# Experiment Log: SIDM Sequential SMAC Benchmark

## Experiment Overview
*   **Goal**: Reproduce RODE (Structural Information Role Discovery) results across all 23 standard SMAC maps.
*   **Method**: RODE (with Structural Entropy-based role discovery).
*   **Status**: 🟢 **IN PROGRESS**
*   **Start Date**: January 29, 2026
*   **Total Target Maps**: 23
*   **Steps per Map**: 2,050,000 (`t_max`)

## Technical Configuration
*   **Environment**: StarCraft II v2.4.10 (Linux 64-bit)
*   **Hardware**: NVIDIA GeForce RTX 3060 (12GB VRAM)
*   **Driver/Libraries**: NVIDIA 560.35.03, CUDA 12.6, EGL enabled
*   **Dependencies**: 
    *   Python 3.12.12
    *   Protobuf 3.20.0 (pinned to avoid descriptor errors)
    *   PyMARL framework (patched for Python 3.12 compatibility)

## Critical Fixes Applied
1.  **SC2 Restoration**: Fixed "Missing .build.info" and missing data files by performing a full, forced unzip of 24,000+ files.
2.  **NumPy Compatibility**: Fixed `AttributeError: module 'numpy' has no attribute 'float'` by replacing `np.float` with `float` in `rode_controller.py` and `sr_controller.py`. This fix prevents the experiment from crashing at the Role Discovery phase (~50k steps).
3.  **YAML Loader**: Switched to `yaml.FullLoader` for PyYAML 6.0 compatibility.
4.  **Collections ABC**: Updated `collections.Mapping` to `collections.abc.Mapping`.

## Progress Tracker

| Map ID | Map Name | Status | Current/Final Step | Notes |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 10m_vs_11m | Completed | 2,050,000 | |
| 2 | 1c3s5z | Completed | 2,050,000 | |
| 3 | 25m | Completed | 2,050,000 | |
| 4 | 27m_vs_30m | Completed | 2,050,000 | |
| 5 | 2c_vs_64zg | Completed | 2,050,000 | |
| 6 | 2m_vs_1z | Running | ~400,000+ | Actively training |
| ... | ... | Pending | 0 | Sequential batch next |

## Monitoring Commands
*   **Global Progress**: `tail -f /workspace/RL/all_maps_batch.log`
*   **Current Map Log**: `tail -f /workspace/RL/sird_run_[CURRENT_MAP].log`
*   **Process Check**: `ps -ef | grep main.py`
