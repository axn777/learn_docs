# 安装Scail-pose库

## 安装 mmpose

```bat
# 手动编译 mmcv main分支，支持 cuda 12.8
# cuda 12.8 需要安装 https://developer.nvidia.com/cuda-12-8-0-download-archive?target_os=Windows&target_arch=x86_64
#			置顶环境变量  
#						C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\bin
#						C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\lib\x64
#			修改系统变量
#						CUDA_HOME：值改为 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8
#						CUDACXX：值改为 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\bin\nvcc.exe


conda deactivate
conda env remove -n openmmlab -y

# ---------------- 5090（sm_120） need cu128  -------------- sm: 流多处理器（Streaming Multiprocessor
# 5090 --------> PyTorch 2.7.0 是首个正式提供 CUDA 12.8 预编译包的稳定版
#           显卡对应架构  CUDA GPU Compute Capability
#           https://pytorch.org/get-started/previous-versions/  查询对应的命令
#			https://download.pytorch.org/whl/cu128/torch/ 查询cu版本下的whl
#      		torch-2.10.0+cu128-cp310-cp310-manylinux_2_28_x86_64.whl  其中cp310-cp310 指的是 cpython从3.10到3.10
# 建议使用 python>=3.10，以保证与 SAMURAI 的兼容性
# SAM2 need python>=3.10, as well as torch>=2.3.1
conda create --name openmmlab python=3.10 -y
conda activate openmmlab

pip install numpy==1.26.4
		pip list | findstr numpy

# conda install pytorch torchvision -c pytorch

# 5090 要求 torch 2.7.0 & cu28
pip install torch==2.7.0 torchvision==0.22.0 torchaudio==2.7.0 --index-url https://download.pytorch.org/whl/cu128
		# 先不考虑 SAM2 的问题了（torch2.3.1），先用 torch2.1.0 cu121 mmcv2.1 mmdet 3.3
		pip install torch==2.1.0 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
		pip install torch==2.1.0 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
		pip install torch==2.3.1 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
		pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu121
		pip install torch==2.2.0 torchvision==0.17.0 torchaudio==2.2.0 --index-url https://download.pytorch.org/whl/cu121
		# pytorch 2.10.0+cu130
		pip install torch torchvision --index-url https://download.pytorch.org/whl/cu130

# openmim 是 OpenMMLab 官方推出的包管理与环境配置工具
# 0.3.9
pip install -U openmim
		# 0.10.7
        mim install mmengine
		mim install mmengine==0.10.4
		
pip install mmengine==0.10.4 opencv-python==4.8.1.78 numpy==1.26.4 packaging==24.0 rich==13.4.2
		

        # see https://github.com/open-mmlab/mmdetection/discussions/12062
        pip install --extra-index-url https://miropsota.github.io/torch_packages_builder mmcv==2.2.0+pt2.7.0cu128 mmdeploy mmdet mmengine

pip install --extra-index-url https://miropsota.github.io/torch_packages_builder mmcv==2.2.0+pt2.7.0cu128 
                ########## mmdet 2.x <=> mmpose 0.x <=> mmcv 1.x
                ########## mmdet 3.x <=> mmpose 1.x <=> mmcv 2.x
                # mim install "mmcv>=2.0.1"   current 2.2.0
                # https://mmcv.readthedocs.io/en/latest/get_started/installation.html#install-with-pip  torch2.3 need mmcv 2.2.0
                # windows：
                #      cuda 12.1   torch 2.1  mmcv 2.1 | mmcv 2.2
                #      cuda 12.1   torch 2.2  mmcv 2.2
                #      cuda 12.1   torch 2.3  mmcv 2.2
                # mmdet 3.2.0 need  mmcv < 2.2.0
                # mmdet 3.3.0 need  mmcv < 2.2.0
                # mmpose 1.3.2 need mmdet 3.2.0

            pip install mmcv==2.1.0 -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.1/index.html
                    mim install mmcv==2.1.0  
            # mim install "mmdet>=3.1.0"  current 3.3.0
                    mim install mmdet==3.2.0
            mim install mmdet==3.3.0
            
            
# 开发分支安装  mmdet 3x
mkdir mmdet
git clone -b dev-3.x https://github.com/open-mmlab/mmdetection.git
cd mmdetection
pip install -r requirements/build.txt
pip install -r requirements/runtime.txt
pip install -e . --no-build-isolation	# -e 编辑模式，源代码本地安装 --no-build-isolation 禁用构建隔离


# Best Practices （Build MMPose from source）
git clone https://github.com/open-mmlab/mmpose.git
cd mmpose
pip install -r requirements.txt
# -e 指的是不打包，直接软链接指向源代码目录
pip install -v -e .

            # ??? 用源码，先手动安装 chumpy
            pip install chumpy --no-build-isolation
            pip install -r requirements.txt
            pip install -v -e .
            
			mim install "mmpose>=1.1.0"
            

# 测试是否安装正确
mim download mmpose --config td-hm_hrnet-w48_8xb32-210e_coco-256x192  --dest .
# 测试失败，numpy.ndarray size changed
# numpy 2.2.6 && opencv-python 4.13.0.90 requires numpy>=2 ---------------- 1.26.4 == 4.9.0.80.
			pip install numpy==2.2.6 opencv-python==4.13.0.90
			pip install numpy==1.26.4 opencv-python==4.9.0.80

        # 从源码安装xtcocoapi（包含xtcocotools）解决兼容性问题
        pip uninstall xtcocotools
        pip install git+https://github.com/jin-s13/xtcocoapi

                    pip uninstall -y numpy
                    pip cache purge
                    pip install numpy==1.26.4 --no-cache-dir -i https://pypi.tuna.tsinghua.edu.cn/simple
                    
# 修改 mmengine/runner/checkpoint.py 348行
# TODO 直接修改 ~/miniconda3/envs/your_env/lib/python3.10/site-packages/ 变成 猴子补丁 或 本地覆盖
checkpoint = torch.load(filename, map_location=map_location, weights_only=False)

# torch版本不能过高，否则报错 ModuleNotFoundError: No module named 'mmcv._ext'
#pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu121
        pip install torch==2.2.0 torchvision==0.17.0 torchaudio==2.2.0 --index-url https://download.pytorch.org/whl/cu121

		pip install mmcv==2.2.0 -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.3/index.html
        
        # 1. 卸载当前numpy 2.2.6
        pip uninstall -y numpy

        # 2. 安装numpy 1.26.4（核心降级）
        pip install numpy==1.26.4 --no-cache-dir -i https://pypi.tuna.tsinghua.edu.cn/simple

        # 3. 降级opencv-python到兼容版本（解决其要求numpy≥2的冲突）
        pip uninstall -y opencv-python
        pip install opencv-python==4.8.1.78 --no-cache-dir -i https://pypi.tuna.tsinghua.edu.cn/simple

python demo/image_demo.py tests/data/coco/000000000785.jpg td-hm_hrnet-w48_8xb32-210e_coco-256x192.py td-hm_hrnet-w48_8xb32-210e_coco-256x192-0e67c616_20220913.pth --out-file vis_results.jpg --draw-heatmap
```

## 安装 scail-pose

```bat
# 进入 scail_pos 目录
cd scail_pos/

# 适配 numpy 1
pip install opencv-python-headless==4.11.0.86

# 安装依赖
pip install -r requirements.txt

# git clone sam2
git clone https://github.com/facebookresearch/sam2.git && cd sam2
pip install -e .
cd ..

# openxlab 0.0.38 requires tqdm~=4.65.0
# sam-2 1.0 requires tqdm>=4.66.1
??? 强制使用 tqdm 4.66.1 ????

# YOLOX: 目标检测模型，负责定位画面中的人体，旷视科技（Megvii），PyTorch 训练，
# DWPose：MMPose 官方主推的轻量型 2D 姿态模型
# NLFPose：MMPose 项目下针对 3D 人体姿态重建的子模型
# wget 需要放入对应环境的Scrpits下
#        pretrained_weights/
#        ├── nlf_l_multi_0.3.2.torchscript
#        └── DWPose/
#            ├── dw-ll_ucoco_384.onnx
#            └── yolox_l.onnx
mkdir pretrained_weights && cd pretrained_weights
wget https://github.com/isarandi/nlf/releases/download/v0.3.2/nlf_l_multi_0.3.2.torchscript
mkdir DWPose
wget -O DWPose/dw-ll_ucoco_384.onnx https://huggingface.co/yzd-v/DWPose/resolve/main/dw-ll_ucoco_384.onnx
wget -O DWPose/yolox_l.onnx https://huggingface.co/yzd-v/DWPose/resolve/main/yolox_l.onnx
cd ..

# 下载 sam2 模型权重
cd sam2/checkpoints 
echo "Downloading sam2.1_hiera_tiny.pt checkpoint..."
wget https://dl.fbaipublicfiles.com/segment_anything_2/092824/sam2.1_hiera_tiny.pt
echo "Downloading sam2.1_hiera_small.pt checkpoint..."
wget https://dl.fbaipublicfiles.com/segment_anything_2/092824/sam2.1_hiera_small.pt
echo "Downloading sam2.1_hiera_base_plus.pt checkpoint..."
wget https://dl.fbaipublicfiles.com/segment_anything_2/092824/sam2.1_hiera_base_plus.pt
echo "Downloading sam2.1_hiera_large.pt checkpoint..
wget https://dl.fbaipublicfiles.com/segment_anything_2/092824/sam2.1_hiera_large.pt

# TODO 安装 mediapipe，可选的，可以不安装？？？
pip install mediapipe numpy=1.26.4

```

## 使用 scail-pose

```bat
# Default Extraction & Rendering:
python NLFPoseExtract/process_pose.py --subdir <path_to_the_example_pair> --resolution 512 896
# Extraction & Rendering using 3D Retarget:
python NLFPoseExtract/process_pose.py --subdir <path_to_the_example_pair> --use_align --resolution 512 896
# Multi-Human Extraction & Rendering:
python NLFPoseExtract/process_pose_multi.py --subdir <path_to_the_example_pair> --resolution 512 896
```



# 安装 Scail 大模型

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
拉取模型 -- **不用 git 代理，Astrill 之前只针对浏览器开启了。。。**

```bat
# 拉取代码时，跳过 Git LFS 大文件的实际下载
set GIT_LFS_SKIP_SMUDGE=1
git clone https://huggingface.co/zai-org/SCAIL-Preview

# 后面可以按需要拉取大文件 
git lfs pull --include="需要的大文件路径/文件名"
# 拉取仓库中所有大文件
git lfs pull

		#git lfs pull --include=Wan2.1_VAE.pth
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

### 运行 SCAIL

```bat
python generate.py --model SCAIL-14B --ckpt_dir SCAIL-Preview --scail_path SCAIL.safetensors --image examples\SCAIL\ref.jpg --pose examples\SCAIL\rendered.mp4 --prompt "the girl is dancing" --target_w 896 --target_h 512
```



# 安装Scail Pos



# 安装Scali Linux（推荐 TODO linux 服务器）