# DRL-for-Mobile-Robot-Navigation-Using-ROS2

<div align="center">
  <img src="/docs/simulation.gif" alt="Simulation" />
</div>

## Table of Contents
- [DRL-for-Mobile-Robot-Navigation-Using-ROS2](#drl-for-mobile-robot-navigation-using-ros2)
  - [Table of Contents](#table-of-contents)
  - [Project Structure](#project-structure)
  - [Requirements](#requirements)
    - [Other requirements](#other-requirements)
  - [Build](#build)
  - [Training](#training)
  - [Testing](#testing)
  - [Additional Demos](#additional-demos)

## Project Structure
```txt
.
├── 📂 docs/: contains demo videos
│   ├── 📄 dynamic_environment.mp4
│   ├── 📄 slam.mp4
│   └── 📄 simulation.mp4
├── 📂 drl_agent/: main deep reinforcement learning agent directory
│   ├── 📂 config/: contains configuration files
│   ├── 📂 launch/: contains launch files
│   ├── 📂 scripts/: contains code for environment, policies, and utilities
│   └── 📂 temp/: stores models, logs, and results
├── 📂 drl_agent_description/: contains robot description files, models, and URDFs
│   ├── 📂 launch/: launch files for agent description
│   ├── 📂 meshes/: 3D models of the robot
│   ├── 📂 models/: contains specific model files for kinect sensors
│   └── 📂 urdf/: URDF files for camera, laser, and robot description
├── 📂 drl_agent_gazebo/: contains Gazebo simulation configuration and world files
│   ├── 📂 config/: simulation and SLAM configuration files
│   ├── 📂 launch/: Gazebo launch files for various setups
│   ├── 📂 models/: Gazebo models used in the simulation
│   └── 📂 worlds/: simulation worlds for training and testing environments
├── 📂 drl_agent_interfaces/: custom action, message, and service definitions
│   ├── 📂 action/: defines DRL session actions
│   ├── 📂 msg/: empty for now
│   └── 📂 srv/: service definitions for environment and robot interactions
├── 📂 velodyne_simulator/: Velodyne LiDAR simulation setup

```

## Requirements
- Install [Ubuntu 22.04](https://www.releases.ubuntu.com/jammy/)
- Install [ROS2 Humble](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)
- Install [Gazebo](https://classic.gazebosim.org/tutorials?tut=install_ubuntu&cat=install)
- Install `gazebo_ros_pkgs` by running:
    ```bash
    sudo apt install ros-humble-gazebo-*
    ```
- Install [PyTorch 2.3.1](https://pytorch.org/get-started/locally/)

### Other requirements
```bash
pip install -r requirements.txt
```

## Build
- Clone this repository:
    ```bash
    mkdir -p ~/drl_agent_ws/src
    cd ~/drl_agent_ws/src
    git clone --recurse-submodules https://github.com/anurye/DRL-for-Mobile-Robot-Navigation-Using-ROS2.git .
    ```
- Install dependencies:
    ```bash
    cd ~/drl_agent_ws
    rosdep install --from-path src -yi --rosdistro humble
    ```
- Build the workspace:
    ```bash
    cd ~/drl_agent_ws
    colcon build
    ```

## Training
- Export the environment variable `DRL_AGENT_SRC_PATH`:
    ```bash
    echo 'export DRL_AGENT_SRC_PATH=~/drl_agent_ws/src/' >> ~/.bashrc
    source ~/.bashrc
    ```
- Launch the simulation:

    Terminal 1:
    ```bash
    cd ~/drl_agent_ws
    source install/setup.bash
    ros2 launch drl_agent_gazebo simulation.launch.py
    ```

  > [!NOTE]
  > If gazebo is not starting, you may want to source it.

    ```bash
    source /usr/share/gazebo/setup.bash 
    ```
    Terminal 2:
    ```bash
    cd ~/drl_agent_ws
    source install/setup.bash
    ros2 run drl_agent environment.py 
    ```

    Terminal 3:
    ```bash
    cd ~/drl_agent_ws
    source install/setup.bash
    ros2 run drl_agent train_td7_agent.py
    ```

## Testing
If you have closed the terminals, restart the simulation in Terminal 1 and Terminal 2 as described above.

Terminal 3:
```bash
cd ~/drl_agent_ws
source install/setup.bash
ros2 run drl_agent test_td7_agent.py
```

## Additional Demos

<table width="100%">
  <tr>
    <td align="center" width="50%">
      <img src="/docs/slam.gif" alt="SLAM" width="90%">
    </td>
    <td align="center" width="50%">
      <img src="/docs/dynamic_environment.gif" alt="Dynamic" width="90%">
    </td>
  </tr>
</table>


要使用 **Docker** 运行 `DRL-for-Mobile-Robot-Navigation-Using-ROS2` 项目，可以借助项目中提供的 **`.devcontainer`** 配置（用于 VS Code Remote - Containers），也可以手动构建和运行容器。以下是两种主流方式：

---

### ✅ 方法一：通过 **VS Code + Dev Container**（推荐，适合开发）

> 适用于在 VS Code 中直接打开项目并自动构建/启动 Docker 容器。

#### 步骤：

1. **安装前提**：
   - 安装 [Docker](https://docs.docker.com/engine/install/)
   - 安装 [VS Code](https://code.visualstudio.com/)
   - 安装 VS Code 扩展：**[Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)**

2. **打开项目**：
   - 在 VS Code 中打开本项目根目录（包含 `.devcontainer/` 文件夹）
   - 按 `F1` → 输入 `Dev Containers: Reopen in Container`
   - VS Code 会自动：
     - 构建镜像（基于 `althack/ros2:humble-full`）
     - 安装 Gazebo 相关包
     - 设置用户为 `ros`
     - 自动 source ROS2 和 Gazebo 环境

3. **在容器内操作**：
   - 终端已配置好 ROS2 环境
   - 可直接运行：
     ```bash
     cd /workspaces/DRL-for-Mobile-Robot-Navigation-Using-ROS2  # 或 ${containerWorkspaceFolder}
     colcon build
     source install/setup.bash
     ros2 launch drl_agent_gazebo simulation.launch.py
     ```

> 💡 GUI 支持：`.devcontainer/devcontainer.json` 已配置 X11/Wayland 转发，Gazebo 界面可正常显示（需本地支持图形显示）。

---

### ✅ 方法二：**手动构建并运行 Docker 容器**

如果你不用 VS Code，可手动操作：

#### 1. 构建镜像
```bash
cd /path/to/project/.devcontainer
docker build -t drl-ros2-humble \
  --build-arg WORKSPACE=/root/drl_agent_ws \
  -f Dockerfile .
```

> 注意：`WORKSPACE` 应与后续挂载的路径一致。

#### 2. 启动容器（带 GUI 支持）
```bash
xhost +local:root  # 允许容器访问 X server（Linux）

docker run -it \
  --network=host \
  --cap-add=SYS_PTRACE \
  --security-opt seccomp=unconfined \
  --security-opt apparmor=unconfined \
  --ipc=host \
  --volume=/tmp/.X11-unix:/tmp/.X11-unix \
  --env DISPLAY=$DISPLAY \
  --env LIBGL_ALWAYS_SOFTWARE=1 \
  --volume $(pwd)/..:/root/drl_agent_ws/src \
  --workdir /root/drl_agent_ws \
  drl-ros2-humble \
  bash
```

> 📝 说明：
> - 将项目目录挂载到容器内的 `/root/drl_agent_ws/src`
> - `LIBGL_ALWAYS_SOFTWARE=1` 启用软件渲染（避免 GPU 驱动问题）
> - 若在 WSL2，需额外配置 X Server（如 VcXsrv）或使用 WSLg

#### 3. 在容器内编译和运行
```bash
# 安装依赖（如有 .repos 文件）
python3 .devcontainer/repos_to_submodules.py  # 将 .repos 转为 submodule（可选）

# 安装 rosdep 依赖
rosdep update
rosdep install --from-path src -y --ignore-src

# 编译
colcon build

# 启动仿真（新开终端或使用 tmux/screen）
source install/setup.bash
ros2 launch drl_agent_gazebo simulation.launch.py
```

---

### 🔧 补充说明

- **`.repos_to_submodules.py`**：  
  该脚本将 `src/` 下的 `.repos` 文件（ROS2 的仓库清单）转换为 Git Submodule，便于版本管理。通常在克隆后运行一次即可。
  
- **GUI 显示问题**：  
  如果 Gazebo 无法启动图形界面，确保：
  - 主机允许 X11 转发（`xhost +`）
  - 容器内设置了 `DISPLAY`
  - 使用了 `--network=host` 和 X11 socket 挂载

- **性能优化**：  
  如需硬件加速（Intel iGPU），取消 `devcontainer.json` 中 `--device=/dev/dri` 的注释，并在 `docker run` 中添加该参数。

---

通过上述任一方法，你都可以在 **隔离、可复现的 Docker 环境**中使用该项目进行训练或测试。

