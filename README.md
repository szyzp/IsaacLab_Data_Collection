# 在 IsaacLab 环境中使用Piper和Nero进行遥操作数据采集

本项目在 [IsaacLab](https://github.com/isaac-sim/IsaacLab) 环境中实现了 **Piper和Nero机械臂的方块堆叠任务**，支持通过遥操作进行人类演示数据的采集与回放，并提供自动化采集数据的脚本用于高效采集演示数据。该项目作为 IsaacLab 的外部项目构建。

[![English](https://img.shields.io/badge/lang-English-blue.svg)](README_EN.md)
[![简体中文](https://img.shields.io/badge/语言-简体中文-red.svg)](README.md)

![Teleop Demonstration](./assets/nero_ik.gif)


## 环境配置

### 1. 安装
- Ubuntu 22.04
- Python 3.11
- Torch 2.7.0
- Cuda 12.8
- IsaacLab v2.3.2
- IsaacSim v5.1.0

下面是Ubuntu 22.04系统的依赖安装：
``` bash
conda create -n isaaclab python=3.11 -y
conda activate isaaclab
pip install --upgrade pip

pip install torch==2.7.0 torchvision==0.22.0 torchaudio==2.7.0 --index-url https://download.pytorch.org/whl/cu128
pip install "isaacsim[all,extscache]==5.1.0" --extra-index-url https://pypi.nvidia.com
isaacsim	# 验证IsaacSim的安装

cd path/to/IsaacLab_path  # 修改为你的IsaacLab安装路径
git clone -b v2.3.2 https://github.com/isaac-sim/IsaacLab.git
sudo apt install cmake build-essential
cd IsaacLab
./isaaclab.sh --install robomimic
python scripts/tutorials/00_sim/create_empty.py	 # 验证IsaacLab的安装

cd path/to/IsaacLab_Data_Collection_paht  # 修改为你的数据采集代码安装路径
git clone https://github.com/szyzp/IsaacLab_Data_Collection.git
cd IsaacLab_Data_Collection
python -m pip install -e source/agx_teleop
```

### 2. 安装可能出现的问题
#### 验证IsaacLab安装时报错
- 报错内容为：`ModuleNotFoundError: No module named 'isaaclab'`
- 使用`conda list isaaclab`命令查看是否有安装框架，但是没有isaaclab子包，输出为：
  ``` bash
  # Name                     Version          Build            Channel
  isaaclab-assets            0.2.4            pypi_0           pypi
  isaaclab-contrib           0.0.2            pypi_0           pypi
  isaaclab-mimic             1.0.16           pypi_0           pypi
  isaaclab-rl                0.4.7            pypi_0           pypi
  isaaclab-tasks             0.11.12          pypi_0           pypi
  ```
- 解决方法：
  ``` bash
  grep -n flatdict source/isaaclab/setup.py	  # 输出 45:    "flatdict==4.0.1",
  sed -i 's/flatdict==4\.0\.1/flatdict==4.1.0/' source/isaaclab/setup.py	# 修改为flatdict==4.1.0
  grep -n flatdict source/isaaclab/setup.py	  # 查看是否修改成功
  ./isaaclab.sh --install robomimic	          # 重新执行安装
  conda list isaaclab	                        # 检查是否有isaaclab
  python scripts/tutorials/00_sim/create_empty.py	 # 重新验证IsaacLab的安装
  ```

## 使用指南

在进行数据采集之前，请确保已创建 `datasets` 文件夹：
```bash
mkdir -p datasets
```

### 手动采集演示数据
使用键盘作为输入设备，手动录制10条成功的演示数据。  
可选任务环境：
- Piper机械臂任务环境：`Isaac-Stack-Cube-Piper-IK-Rel-v0`
- Nero机械臂任务环境：`Isaac-Stack-Cube-Nero-IK-Rel-v0`
```bash
python scripts/tools/record_demos.py \
    --task Isaac-Stack-Cube-Piper-IK-Rel-v0 \
    --device cpu \
    --teleop_device keyboard \
    --dataset_file ./datasets/dataset.hdf5 \
    --num_demos 10
```
*(可选遥操作设备 `--teleop_device`: `keyboard`, `spacemouse`, `handtracking`)*

### 自动采集演示数据
``` bash
python scripts/tools/record_ik_stack.py \
  --task Isaac-Stack-Cube-Piper-IK-Rel-v0 \
  --device cuda \
  --dataset_file ./datasets/ik_dataset.hdf5 \
  --num_demos 10
```
*(可选`--headless`参数在无可视化服务器自动采集演示数据)*

### 回放演示数据
回放并验证刚才收集的`.hdf5`数据集。
```bash
python scripts/tools/replay_demos.py \
    --task Isaac-Stack-Cube-Piper-IK-Rel-v0 \
    --device cpu \
    --dataset_file ./datasets/dataset.hdf5
```

## 键盘遥操作控制方法

如果在启动录制时使用 `--teleop_device keyboard`，请在运行环境处于激活状态时使用以下按键控制机械臂（基于 SE(3) 控制）：

| 操作 | 快捷键 |
| :--- | :---: |
| **重置所有指令** | `R` |
| **开关夹爪**    | `K` |
| **沿 X 轴移动** | `W` / `S` |
| **沿 Y 轴移动** | `A` / `D` |
| **沿 Z 轴移动** | `Q` / `E` |
| **绕 X 轴旋转** | `Z` / `X` |
| **绕 Y 轴旋转** | `T` / `G` |
| **绕 Z 轴旋转** | `C` / `V` |

> **回放时的快捷键**：
> - 暂停回放: `B`
> - 恢复回放: `N`


## 参考项目
1、[IsaacLab官方教程](https://isaac-sim.github.io/IsaacLab/main/index.html)  
2、[IsaacLab](https://github.com/isaac-sim/IsaacLab)