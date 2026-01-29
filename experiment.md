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

| Map ID | Map Name | Status | Console Log (Clickable) | Sacred Result (Clickable) |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 10m_vs_11m | Running | [Log](../sird_run_10m_vs_11m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 2 | 1c3s5z | Pending | [Log](../sird_run_1c3s5z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 3 | 25m | Pending | [Log](../sird_run_25m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 4 | 27m_vs_30m | Pending | [Log](../sird_run_27m_vs_30m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 5 | 2c_vs_64zg | Pending | [Log](../sird_run_2c_vs_64zg.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 6 | 2m_vs_1z | Pending | [Log](../sird_run_2m_vs_1z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 7 | 2s3z | Pending | [Log](../sird_run_2s3z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 8 | 2s_vs_1sc | Pending | [Log](../sird_run_2s_vs_1sc.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 9 | 3m | Pending | [Log](../sird_run_3m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 10 | 3s5z | Pending | [Log](../sird_run_3s5z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 11 | 3s5z_vs_3s6z | Pending | [Log](../sird_run_3s5z_vs_3s6z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 12 | 3s_vs_3z | Pending | [Log](../sird_run_3s_vs_3z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 13 | 3s_vs_4z | Pending | [Log](../sird_run_3s_vs_4z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 14 | 3s_vs_5z | Pending | [Log](../sird_run_3s_vs_5z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 15 | 5m_vs_6m | Pending | [Log](../sird_run_5m_vs_6m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 16 | 6h_vs_8z | Pending | [Log](../sird_run_6h_vs_8z.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 17 | 8m | Pending | [Log](../sird_run_8m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 18 | 8m_vs_9m | Pending | [Log](../sird_run_8m_vs_9m.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 19 | MMM | Pending | [Log](../sird_run_MMM.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 20 | MMM2 | Pending | [Log](../sird_run_MMM2.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 21 | bane_vs_bane | Pending | [Log](../sird_run_bane_vs_bane.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 22 | corridor | Pending | [Log](../sird_run_corridor.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |
| 23 | so_many_baneling | Pending | [Log](../sird_run_so_many_baneling.log) | [Sacred Result](../SIDM/SIRD/results/sacred/) |

## Monitoring Commands
*   **Global Progress**: [all_maps_batch.log](../all_maps_batch.log)
*   **Active Map Process**: `ps -ef | grep main.py`
