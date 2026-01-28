# Scail 模型

https://huggingface.co/zai-org/SCAIL-Preview/tree/main

```
SCAIL-Preview/
├── models_clip_open-clip-xlm-roberta-large-vit-huge-14-onlyvisual.pth
├── Wan2.1_VAE.pth
├── model
│   ├── 1
│   │   └── mp_rank_00_model_states.pt
│   └── latest
└── umt5-xxl
    ├── ...
```

### models_clip_open-clip-xlm-roberta-large-vit-huge-14-onlyvisual.pth

+ models_clip：clip模型体系，openAI，对比语言图像预训练
+ open-clip：基于openclip项目
+ xlm-roberta-large：【文本编码器】支持100+语言，鲁棒性强，大参数量的通用文本编码器
  + xml: cross-lingual language model: 跨语言语言模型
  + roberta: 鲁棒优化的BERT
  + large: 参数巨大
+ vit-huge-14: 
  + vit: 视觉 Transformer
  + huge: 规模大（大于large）
  + 14指patch size, 14*14的图像切块尺寸
+ onlyvisual
  + 只包含视觉侧（Visual）的权重

### wan2.1_VAE.pth

+ VAE: 变分自编码 ----------- 带概率分布约束的自编码
+ 

# 安装Scail Windows（wan branch）

### conda 和 python

+ conda 

  1. 安装 miniconda3

     ```bash
     nvidia-smi
        # CUDA Version: 13.0 
     ```

  2. 安装清华源

     ```bash
     #添加镜像源
     conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
     conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
     conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
     conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
     conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
     
     #终端显示包从哪个channel下载，以及下载地址是什么
     conda config --set show_channel_urls yes
     ```

  3. 创建python环境

     ```bash
     # 删除原来的scail环境（可选）
     conda env remove -n scail
     # 创建scail环境，python版本 3.12
     conda create -n scail python=3.12 -y
     # 进入环境
     conda activate scail
     ```

### 安装14B模型，通过 HuggingFace

~~配置 git 代理~~

```bat
# 配置 HTTPS 代理（核心，对应 443 端口访问）
git config --global http.https://huggingface.co.proxy http://127.0.0.1:7890
git config --global https.https://huggingface.co.proxy http://127.0.0.1:7890

# （可选）若克隆其他 GitHub/GitLab 仓库也需要代理，可配置全局代理
# git config --global http.proxy http://127.0.0.1:7890
# git config --global https.proxy http://127.0.0.1:7890

# 取消 Hugging Face 专属代理
git config --global --unset http.https://huggingface.co.proxy
git config --global --unset https.https://huggingface.co.proxy

# 取消全局代理（若之前配置过）
# git config --global --unset http.proxy
# git config --global --unset https.https://huggingface.co.proxy

# 查看是否生效
git config --global --list
```

拉取模型 -- **不用 git 代理，Astrill 之前只针对浏览器开启了。。。**

```bat
# 拉取代码时，跳过 Git LFS 大文件的实际下载
set GIT_LFS_SKIP_SMUDGE=1
git clone https://huggingface.co/zai-org/SCAIL-Preview

# 后面可以按需要拉取大文件 
git lfs pull --include="需要的大文件路径/文件名"
# 拉取仓库中所有大文件
git lfs pull
```

### torch 安装     

+ 显卡相关信息和配置

  + 安装 cuda (nvcc: nvidia cuda compiler)

    + 查看Nvidia显卡驱动信息

    + 空白处鼠标右键，NVIDIA 控制面板

    + 左下角 系统信息

    + 组件，NVCUDA64.dll  ---------------  **CUDA Driver 13.0.48 driver**

      +   ---> 选择安装 13.0 以下版本的 Cuda
      + 安装CUDA Toolkit **13.0**

      ```bash
      # 查看版本信息
      nvcc --version
      ```

  + cuDNN 9.18.1 （深度神经网络的GPU加速库）

    + exe 安装 cudnn 9.18.1
    + 选择精简安装

+ pytorch 安装

  + git hub release 看，最新版本 2.10.0
  + 我们选择 2.9.1 版本
    + **PyTorch**：2.9.1+cu130（CUDA runtime 13.0）

```bash
# PyTorch 版本： 2.9.1+cu130
pip install torch==2.9.1 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu130
```

```python
import torch

device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

#1. pytorch 版本
print("PyTorch 版本：", torch.__version__)
#2. 设备
print("设备0：", device)
#3. CUDA版本
print("CUDA 版本：", torch.version.cuda)
#4. cuDNN 版本 ------------> 91200
print("cuDNN 版本：", torch.backends.cudnn.version())

exit()
```

### requirements.txt 安装

+ 直接安装，会报错，**ModuleNotFoundError: No module named 'torch'**

  ```bash
  pip uninstall -y flash_attn
  
  # 指定清华源，会快很多，但是这里也出错，还是torch没有安装的问题
  pip install flash-attn --no-build-isolation -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```
### 单独安装 flash_attn

+ 下载预编译好的whl安装，torch和cuda虽然可能一致，但是显卡对应的CUDA架构不一致，编译者可能只开启了他的显卡的编译选项

  + `CUDA_HOME`: Path to your CUDA toolkit.

    - `FLASH_ATTN_CUDA_ARCHS`: GPU architecture (e.g. `86` for RTX 3070).

    - `MAX_JOBS`: Parallel build jobs (lower if you run out of RAM).

    - clone 下载 wheel

  + 问题是只支持它所指定的显卡，比如3090

+ **显卡对应的版本**，编译的时候需要指定适合自己显卡的版本，[所以需要自己编译]()

  + **5090 需要设置 set "FLASH_ATTN_CUDA_ARCHS=120"**

  + [CUDA GPU Compute Capability | NVIDIA Developer](https://developer.nvidia.com/cuda/gpus)

    | Compute Capability | Data Center                                                  |
    | ------------------ | ------------------------------------------------------------ |
    | 12.1               | NVIDIA GB10 (DGX Spark)                                      |
    | **12.0**           | NVIDIA RTX PRO 6000 Blackwell，RTX 5050-5090，RTX Pro 2000-5000 Blackwell |
    | 11.0               | Jetson T5000 Jetson T4000                                    |
    | 10.3               | NVIDIA GB300<br/>NVIDIA B300                                 |
    | 9.0                | NVIDIA GH200<br/>NVIDIA H200<br/>NVIDIA H100                 |
    | 8.9                | NVIDIA RTX 2000 - 6000 Ada<br/>GeForce RTX 4050 - 4090       |
    | 8.6                | NVIDIA RTX A2000 -  A6000<br/>GeForce RTX 3050 - 3090 Ti     |
    | 7.5                | QUADRO RTX 3000 - QUADRO RTX 8000<br/>QUADRO T2000<br/>NVIDIA T400-1200<br/>GeForce GTX 1650 Ti<br/>NVIDIA TITAN RTX<br/>GeForce RTX 2060 - 2080 Ti |

+ clone flash_attn 库，需要注意修改长文件名策略（win + git）

  + **Win11** 右键 - 系统 - 高级 - 启用长路径
  + git config --global core.longpaths true

+ 切换到分支

  ```bat
  # 切换tag
  git checkout v2.8.3
  ```

+ 需要 vs studio 2022版本才行, 2026不行

  ```bat
  # 设置windows 开发者环境
  # 注意 Only the versions between 2019 and 2022 (inclusive) are supported! 需要用 2022版本，
  "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Visual Studio\Visual Studio Tools\Developer Command Prompt for VS.lnk" -arch=amd64 -host_arch=amd64
  
  # Windows PowerShell 使用 winget 安装
  winget show --versions "Microsoft.VisualStudio.2022.Community"
  # 版本选择了 17.14.25
  winget install -v <具体版本号> "Microsoft.VisualStudio.2022.Community"
  # 进入Installer，安装VC桌面版，否则没有 cl.exe
  
  "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Visual Studio 2022\Visual Studio Tools\Developer Command Prompt for VS 2022.lnk" -arch=amd64 -host_arch=amd64
  ```

+ 切换到分支

```bat
set DISTUTILS_USE_SDK=1			# 消除 VC 环境重复激活警告
set MAX_JOBS=1 					# 限制并行，防止内存爆掉 ------- 128G 设置 2 安全 ???
set NVCC_THREADS=2  			# 限制线程数量
set TORCH_CUDA_ARCH_LIST=12.0 	# 只编译 5090 

# 开始编译
python setup.py clean --all
python setup.py bdist_wheel
```

+ 通过whl安装

  ```bat
  pip install flash_attn-2.8.3-cp312-cp312-win_amd64.whl
  ```

# 安装Scail Pos



# 安装Scali Linux（推荐 TODO linux 服务器）
