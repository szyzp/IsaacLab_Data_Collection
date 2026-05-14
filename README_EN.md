# Teleoperation Data Collection using Piper in IsaacLab Environment

This project implements the **cube stacking (Stack) task for the Piper robotic arm** within the [IsaacLab](https://github.com/isaac-sim/IsaacLab) environment, and supports the collection and playback of human demonstration data via teleoperation. This project is built as an External Project of IsaacLab.

![Teleop Demonstration](./assets/teleop.gif)


## Installation

### 1. Prerequisites
- Isaac Sim and IsaacLab have been installed and configured.
- The corresponding Conda virtual environment named `isaaclab` for IsaacLab is installed.

### 2. Install the Project
``` bash
git clone https://github.com/szyzp/agx_teleop.git
cd agx_teleop
python -m pip install -e source/agx_teleop
```

## Usage Guide

Before data collection, please ensure that the `datasets` folder has been created:
```bash
mkdir -p datasets
```

### Record Demonstration Data
Use the keyboard as the input device to record 10 successful demonstrations:
```bash
python scripts/tools/record_demos.py \
    --task Isaac-Stack-Cube-Piper-IK-Rel-v0 \
    --device cpu \
    --teleop_device keyboard \
    --dataset_file ./datasets/dataset.hdf5 \
    --num_demos 10
```
*(Optional teleoperation devices for `--teleop_device`: `keyboard`, `spacemouse`, `handtracking`)*

### Replay Demonstration Data
Replay and verify the dataset you just collected:
```bash
python scripts/tools/replay_demos.py \
    --task Isaac-Stack-Cube-Piper-IK-Rel-v0 \
    --device cpu \
    --dataset_file ./datasets/dataset.hdf5
```

## Keyboard Teleoperation Controls

If you use `--teleop_device keyboard` when starting the recording, please use the following keys to control the robotic arm while the runtime environment is active (based on SE(3) control):

| Operation | Shortcut |
| :--- | :---: |
| **Reset all commands** | `R` |
| **Open/Close gripper** | `K` |
| **Move along X-axis** | `W` / `S` |
| **Move along Y-axis** | `A` / `D` |
| **Move along Z-axis** | `Q` / `E` |
| **Rotate around X-axis** | `Z` / `X` |
| **Rotate around Y-axis** | `T` / `G` |
| **Rotate around Z-axis** | `C` / `V` |

> **Shortcuts during playback**:
> - Pause playback: `B`
> - Resume playback: `N`