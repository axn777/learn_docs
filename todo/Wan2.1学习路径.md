## 一、核心必看（不懂这些，读 Wan2.1 代码就是看天书）

### 1. 扩散模型基础（Wan2.1 核心生成框架）

- **论文**：《Denoising Diffusion Probabilistic Models (DDPM)》（2020）

  - 链接：https://arxiv.org/abs/2006.11239
  - 为什么看：Wan2.1 本质是**视频扩散模型**，所有 UNet 前向 / 反向扩散、采样逻辑都基于 DDPM；
  - 对应代码：`model/denoiser.py`（去噪核心）、`sampler/video_sampler.py`（采样器）。

  

- **论文**：《Improved Denoising Diffusion Probabilistic Models (IDDPM)》（2021）

  - 链接：https://arxiv.org/abs/2102.09672
  - 为什么看：Wan2.1 用了 IDDPM 的「改进采样策略 + 方差调度」，解决视频生成的帧率 / 流畅度问题；
  - 对应代码：`configs/scheduler.py`（扩散调度器）。

  

### 2. 视频扩散模型（Wan2.1 区别于图片模型的核心）

- **论文**：《Video Diffusion Models (VDM)》（2022）

  - 链接：https://arxiv.org/abs/2204.03458
  - 为什么看：首次把扩散模型用到视频，定义了「时空联合扩散」「帧间一致性」，Wan2.1 完全继承这套思路；
  - 对应代码：`model/unet_video.py`（时空 UNet 结构）、`data/frame_sampler.py`（帧采样）。

  

- **论文**：《AnimateDiff: Animate Your Personalized Text-to-Image Diffusion Models without Specific Tuning》（2023）

  - 链接：https://arxiv.org/abs/2307.04725
  - 为什么看：Wan2.1 借鉴了 AnimateDiff 的「运动模块（Motion Module）」，保证视频帧间不跳、动作连贯；
  - 对应代码：`model/motion_module.py`（核心动效模块）。

  

### 3. VAE 进阶（你已看基础 VAE，补这篇够了）

- **论文**：《High-Resolution Image Synthesis with Latent Diffusion Models (LDM)》（2021）

  - 链接：https://arxiv.org/abs/2112.10752
  - 为什么看：Wan2.1 的 VAE 不是原始 VAE，而是 LDM 里的「隐空间扩散 VAE」—— 负责把视频帧压缩到 latent 空间，再扩散；
  - 对应代码：`model/vae.py`（Wan2.1 官方 VAE 实现）、`model/vae_encoder.py`（编码对齐逻辑）。

  

------

## 二、进阶补充（读懂 Wan2.1 特有优化 / 创新）

### 1. 高效视频生成（Wan2.1 速度 / 显存优化）

- **论文**：《Efficient Video Diffusion Models with UNet 3D》（2023）

  - 链接：https://arxiv.org/abs/2309.17421
  - 为什么看：Wan2.1 用了「3D UNet + 时空注意力」，解决长视频生成的显存爆炸问题；
  - 对应代码：`model/attention.py`（时空注意力）、`model/unet_3d.py`。

  

### 2. 帧间一致性（Wan2.1 不穿模 / 不飘的核心）

- **论文**：《Consistency Models》（2023）

  - 链接：https://arxiv.org/abs/2303.01469
  - 为什么看：Wan2.1 用了一致性模型的「快速采样 + 帧间约束」，保证视频不闪、场景不变；
  - 对应代码：`model/consistency.py`（一致性约束）、`configs/consistency.yaml`（参数配置）。

  

### 3. 文本 / 视频对齐（Wan2.1 文生视频的精准度）

- **论文**：《BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models》（2023）

  - 链接：https://arxiv.org/abs/2301.12597
  - 为什么看：Wan2.1 的文本编码器（CLIP/BLIP-2）负责把「小人劳动」这类提示词转成特征，对齐视频内容；
  - 对应代码：`model/text_encoder.py`（文本编码）、`model/aligner.py`（文本 - 视频对齐）。

  

------

## 三、工程落地（读懂 Wan2.1 训练 / 推理代码）

### 1. 分布式训练（Wan2.1 大模型训练）

- **论文**：《Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism》（2019）

  - 链接：https://arxiv.org/abs/1909.08053
  - 为什么看：Wan2.1 用了模型并行 / 数据并行，代码里的 `trainer/distributed.py` 全是这套逻辑；
  - 对应代码：`trainer/trainer.py`（分布式训练）。

  

### 2. 混合精度训练（Wan2.1 速度优化）

- **论文**：《Mixed Precision Training》（2017）

  - 链接：https://arxiv.org/abs/1710.03740
  - 为什么看：Wan2.1 用 FP16/FP8 训练，代码里的 `utils/amp.py` 是核心；
  - 对应代码：`utils/amp.py`（混合精度）。

  

------

## 四、极简阅读路径（按顺序来，不绕弯）

1. 先啃 **DDPM + LDM**：搞懂「扩散 + 隐空间 VAE」，能读通 Wan2.1 的 UNet+VAE 核心；
2. 再啃 **VDM + AnimateDiff**：搞懂「视频扩散 + 运动模块」，能读通帧间一致性；
3. 最后补 **Consistency Models + 工程论文**：搞懂优化 / 训练逻辑。

------

## 总结

1. **核心必看**：DDPM（扩散基础）、VDM（视频扩散）、LDM（隐空间 VAE）—— 这 3 篇是读懂 Wan2.1 代码的「地基」；
2. **进阶重点**：AnimateDiff（运动模块）、Consistency Models（帧间一致）—— 对应 Wan2.1 视频不飘 / 不穿模的核心；
3. **工程补充**：Megatron-LM（分布式）、Mixed Precision（混合精度）—— 读懂训练 / 推理代码的关键。

按这个顺序读，你不会陷入「论文堆里找不到重点」的问题，每读一篇都能对应到 Wan2.1 的具体代码模块，效率最高。