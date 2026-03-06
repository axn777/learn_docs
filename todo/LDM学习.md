

## 一、学习路径总览

| 阶段         | 核心目标                              | 关键产出                                           |
| ------------ | ------------------------------------- | -------------------------------------------------- |
| **基础铺垫** | 掌握扩散模型数学与实现核心            | 能跑通 DDPM/DDIM 采样，理解噪声调度与去噪逻辑      |
| **核心 LDM** | 吃透 “VAE + 潜在扩散 + 条件引导” 架构 | 复现 LDM 文生图，理解 Cross-Attention 注入机制     |
| **工程落地** | 适配工业级训练 / 推理与部署           | 用 Diffusers 做微调、量化、ONNX 导出，适配业务场景 |
| **前沿进阶** | 跟进视频 / 3D / 高效生成方向          | 理解 DiT、SDXL、Seedance 等变体的创新点            |

------

## 二、必读论文（分阶段，附核心价值）

### 1. 基础铺垫（扩散模型根基）

| 论文标题                                        | arxiv 编号 | 核心价值                                                 |
| ----------------------------------------------- | ---------- | -------------------------------------------------------- |
| Denoising Diffusion Probabilistic Models (DDPM) | 2006.11239 | 扩散模型奠基，前向 / 反向过程数学定义，MSE 损失核心      |
| Denoising Diffusion Implicit Models (DDIM)      | 2010.02502 | 采样步数大幅减少（从 1000→50），加速推理，工业级采样基础 |

### 2. 核心 LDM（必读核心）

| 论文标题                                                     | arxiv 编号 | 核心价值                                                     |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| High-Resolution Image Synthesis with Latent Diffusion Models | 2112.10752 | **LDM 开山之作**，Stable Diffusion 原型；VAE 潜空间设计、Cross-Attention 条件引导、多任务统一框架 |

### 3. 工程与进阶（适配落地与前沿）

| 论文标题                                                     | arxiv 编号 | 核心价值                                                     |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| SDXL: Improving Latent Diffusion Models for High-Resolution Image Synthesis | 2307.01952 | 高分辨率（1024×1024）LDM，双文本编码器、更大潜空间，提升细节与语义对齐 |
| Adversarial Diffusion Distillation (SDXL-Turbo)              | 2311.17042 | 极速采样（1–4 步），蒸馏技术落地，适合低延迟场景             |
| Scalable Diffusion Models with Transformers (DiT)            | 2212.09748 | 用 Transformer 替代 U-Net，为 SD3、Seedance 等视频模型奠定基础 |
| Seedance 2.0 Technical Report                                | 官方发布   | 视频 LDM 标杆，双分支 DiT（语义 + 时空）、音画联合生成，潜空间时序一致性解决方案 |

------

## 三、源码推荐（分场景，附使用建议）

### 1. 核心 LDM 复现（学术向，理解底层）

- **CompVis/latent-diffusion**（官方）

  🔗 https://github.com/CompVis/latent-diffusion

  ✅ 特点：LDM 论文官方实现，支持 VAE 单独训练、潜空间扩散训练，配置文件驱动（类似 OpenMMLab），适合研究潜空间设计与扩散过程。

  📌 入门入口：`configs/latent-diffusion/text2img256.yaml`（文生图基础配置），`main.py` 训练入口。

### 2. 工程化开发（生产向，快速落地）

- **Hugging Face Diffusers**（首选）

  🔗 https://github.com/huggingface/diffusers

  ✅ 特点：模块化封装（Models/Schedulers/Pipelines），支持一键加载 SD/SDXL/Seedance，内置微调、量化、ONNX 导出，适配 Windows+Miniconda 环境。

  - 姿态估计引导生成：结合`controlnet`分支，支持 COCO/DWPose 关键点注入。
  - 部署优化：`diffusers.onnx`模块一键转 ONNX，适配推理引擎。
  - 多模态：无缝集成 CLIP、BLIP 等文本 / 图像编码器。

- **Stability-AI/generative-models**

  🔗 https://github.com/Stability-AI/generative-models

  ✅ 特点：SDXL/SDXL-Turbo 官方实现，包含高分辨率 VAE、双文本编码器架构，适合研究大尺寸生成与蒸馏技术。

### 3. 视频 LDM（适配 Seedance 研究）

- **Stability-AI/stable-video-diffusion**

  https://github.com/Stability-AI/stable-video-diffusion

  ✅ 特点：图像到视频 LDM，潜空间时序建模，可作为理解 Seedance 双分支架构的简化版参考。

- **字节跳动 Seedance 官方代码**（待开源）

  ✅ 关注官方仓库，目前可通过 Diffusers 的

  ```
  seedance
  ```

   pipeline 体验推理，适合对比 DiT 与 U-Net 在视频中的差异。

### 4. 轻量化与部署（适配你的硬件需求）

- **AUTOMATIC1111/stable-diffusion-webui**

  https://github.com/AUTOMATIC1111/stable-diffusion-webui

  ✅ 特点：插件生态丰富（ControlNet、OpenPose、ONNX Runtime），适合快速验证姿态估计引导生成的效果，支持 Windows 本地部署。

## 四、分步实践指南（适配你的技术栈）

### 阶段 1：环境准备（Windows+Miniconda）

1. 新建环境：`conda create -n ldm python=3.10`，`conda activate ldm`。

2. 安装依赖：

   ```
   pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
   pip install diffusers transformers accelerate onnxruntime-gpu opencv-python numpy
   ```

   

3. 验证：运行 Diffusers 的 SD 文生图示例，确保 CUDA 与 ONNX Runtime 正常。

### 阶段 2：核心 LDM 理解（从代码到数学）

1. **VAE 单独训练**：用 CompVis 仓库，运行`configs/autoencoder/autoencoder_kl_8x8x64.yaml`，理解潜空间下采样（8 倍）与 KL 正则化。
2. **扩散过程复现**：用 PyTorch 实现 DDPM 的`q_sample`（前向加噪）与`p_sample`（反向去噪），对比 DDIM 的采样速度。
3. **条件引导**：在 U-Net 中添加 Cross-Attention 层，注入 CLIP 文本嵌入，理解语义如何引导潜空间生成。

### 阶段 3：工程落地（姿态估计 + LDM）

1. **姿态提取**：用 OpenPose/DWPose 提取人体关键点，转为 ControlNet 条件张量。
2. **引导生成**：用 Diffusers 的`StableDiffusionControlNetPipeline`，加载 SD 模型与 ControlNet-Pose 权重，实现姿态可控生成。
3. **部署优化**：将模型转为 ONNX 格式，用`onnxruntime-gpu`推理，对比显存占用与速度。

### 阶段 4：前沿进阶（视频 LDM 与 DiT）

1. **DiT 理解**：阅读 DiT 论文，用`facebookresearch/DiT`仓库复现图像生成，对比 U-Net 与 Transformer 的去噪差异。
2. **Seedance 分析**：用 Diffusers 加载 Seedance 模型，分析其双分支输出（语义潜码 + 时空动态潜码），理解时序一致性设计。

## 五、关键学习资源（补充）

1. **课程**：DeepLearning.AI《Diffusion Models》专项课程（理论 + 代码），Hugging Face《Diffusers Cookbook》（实战 notebooks）。
2. **论文合集**：https://github.com/zelaki/Reading-Diffusion（扩散模型论文汇总，含 LDM 与视频扩散）。
3. **中文解读**：CompVis LDM 论文的 CSDN 精读（含逐行代码解析），适合快速理解核心模块。

## 六、避坑建议（针对你的场景）

1. **潜空间维度**：LDM 的 VAE 下采样率通常为 8（512→64），SDXL 为 4（1024→256），注意不同模型的潜空间尺寸匹配。
2. **显存优化**：Windows 下用`accelerate`库的`--mixed_precision fp16`，推理时用`enable_attention_slicing`，降低显存占用。
3. **姿态引导**：确保关键点坐标归一化到 [0,1]，与 ControlNet 的输入格式一致，避免生成结果错位。

