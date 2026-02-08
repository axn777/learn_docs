## ~~安装客户端版本~~

### 安装稳定版本



### 使用uv库编写（rust），如何跟Conda配合呢

## git安装

### 安装步骤

#### 1. 安装 python & torch

```bat
conda create -n comfyui python=3.12 -y
conda activate comfyui

# nvidia-smi(显卡驱动)：显示当前的CUDA版本
# 目前nvcc（CUDA驱动，非显卡驱动）是130，暂时安装 cu128 适配
pip install torch==2.7.0 torchvision==0.22.0 torchaudio==2.7.0 --index-url https://download.pytorch.org/whl/cu128
```



#### 2. 安装分支

```bat
# 1. 克隆主仓库（先不指定版本）
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI

# 2. 拉取远程所有tag（确保能找到0.11.1）
git fetch origin --tags

# 3. 切换到tag v0.11.1（关键：tag需用完整的检出命令）
git checkout tags/v0.11.1 -b 0.11.1
```

#### 

#### 3. 安装依赖

```bat
pip install -r requirements.txt
```



#### 4. 安装 comfyui-manager

```bat
# 安装managerpython main.py --enable-manager
pip install -r manager_requirements.txt

# 进入目录
cd custom_nodes

# 下载
git clone https://github.com/ltdrdata/ComfyUI-Manager comfyui-manager

			# 带着manager启动
			python main.py --enable-manager
# 貌似 ComfyUIManager 跟默认的有冲突
python main.py
```



## TODO 插件
### EasyAnimate



## Shortcuts



| Keybind                                | Explanation                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| `Ctrl` + `Enter`                       | Queue up current graph for generation                        |
| `Ctrl` + `Shift` + `Enter`             | Queue up current graph as first for generation               |
| `Ctrl` + `Alt` + `Enter`               | Cancel current generation                                    |
| `Ctrl` + `Z`/`Ctrl` + `Y`              | Undo/Redo                                                    |
| `Ctrl` + `S`                           | Save workflow                                                |
| `Ctrl` + `O`                           | Load workflow                                                |
| `Ctrl` + `A`                           | Select all nodes                                             |
| `Alt `+ `C`                            | Collapse/uncollapse selected nodes                           |
| `Ctrl` + `M`                           | Mute/unmute selected nodes                                   |
| `Ctrl` + `B`                           | Bypass selected nodes (acts like the node was removed from the graph and the wires reconnected through) |
| `Delete`/`Backspace`                   | Delete selected nodes                                        |
| `Ctrl` + `Backspace`                   | Delete the current graph                                     |
| `Space`                                | Move the canvas around when held and moving the cursor       |
| `Ctrl`/`Shift` + `Click`               | Add clicked node to selection                                |
| `Ctrl` + `C`/`Ctrl` + `V`              | Copy and paste selected nodes (without maintaining connections to outputs of unselected nodes) |
| `Ctrl` + `C`/`Ctrl` + `Shift` + `V`    | Copy and paste selected nodes (maintaining connections from outputs of unselected nodes to inputs of pasted nodes) |
| `Shift` + `Drag`                       | Move multiple selected nodes at the same time                |
| `Ctrl` + `D`                           | Load default graph                                           |
| `Alt` + `+`                            | Canvas Zoom in                                               |
| `Alt` + `-`                            | Canvas Zoom out                                              |
| `Ctrl` + `Shift` + LMB + Vertical drag | Canvas Zoom in/out                                           |
| `P`                                    | Pin/Unpin selected nodes                                     |
| `Ctrl` + `G`                           | Group selected nodes                                         |
| `Q`                                    | Toggle visibility of the queue                               |
| `H`                                    | Toggle visibility of history                                 |
| `R`                                    | Refresh graph                                                |
| `F`                                    | Show/Hide menu                                               |
| `.`                                    | Fit view to selection (Whole graph when nothing is selected) |
| Double-Click LMB                       | Open node quick search palette                               |
| `Shift` + Drag                         | Move multiple wires at once                                  |
| `Ctrl` + `Alt` + LMB                   | Disconnect all wires from clicked slot                       |

`Ctrl` can also be replaced with `Cmd` instead for macOS users
