---
layout: post
title: InternLM2 Lesson 4 - XTuner微调LLM 1.8B 多模态
---

讲师：李剑锋 汪周谦 王群|XTuner社区贡献者
[视频地址](https://www.bilibili.com/video/BV15m421j78d/)
[文档](https://github.com/InternLM/Tutorial/blob/camp2/xtuner/personal_assistant_document.md)

## 微调
![image.png](https://s2.loli.net/2024/04/15/1slCPOjh7DpYGti.png)

![image.png](https://s2.loli.net/2024/04/15/9cXiFqr6umMRY3s.png)

## XTuner
优化加速：Flash Attention，DeepSpeed ZeRO，Pytorch FSDP

Flash Attention 和 DeepSpeed ZeRO 是 XTuner 最重要的两个优化技巧
- FIash Attention：将 Attention 计算并行化，避免了计算过程中 Attention Score NxN的显存占用（训练过程中的 N 都比较大）
    ![image.png](https://s2.loli.net/2024/04/15/cef2156NwTHmgdF.png)
- ZeRO 优化：通过将训练过程中的参数、梯度和优化器状态切片保存，能够在多 GPU 训练时显著节省显存；除了将训练中间状态切片外， DeepSpeed 训练时使用 FP16 的权重，相较于 Pytorch 的 AMP 训练，在单 GPU 上也能大幅节省显存
    ![image.png](https://s2.loli.net/2024/04/15/gcHjVs891QLhNoJ.png)


## 实战
### 微调个人小助手
![image.png](https://s2.loli.net/2024/04/15/a7DHxr4QcSIXzLq.png)

![image.png](https://s2.loli.net/2024/04/15/pmF57dKJNOiLE3G.png)

![image.png](https://s2.loli.net/2024/04/15/QyE2Jd9jPpmUqc1.png)

![image.png](https://s2.loli.net/2024/04/15/WHVKhBwakciF6xM.png)


## 多模态
![image.png](https://s2.loli.net/2024/04/15/XWm3eS8MN4xj7hc.png)

### LLaVA方案
![image.png](https://s2.loli.net/2024/04/15/mOkLqeQi4lSKJBX.png)

![image.png](https://s2.loli.net/2024/04/15/5xyFbZWY2qTtO8c.png)

#### Finetune
Finetune前
![image.png](https://s2.loli.net/2024/04/16/Xq83eLKimSV2npJ.png)

```bash
xtuner copy-cfg \
  llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune \
  /root/tutorial/xtuner/llava
# visual encoder是openai的clip-vit-large-patch14-336
# 训练数据来自GPT生成的图片问答对

xtuner train /root/tutorial/xtuner/llava/llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune_copy.py --deepspeed deepspeed_zero2

```

Finetune后
![image.png](https://s2.loli.net/2024/04/16/Y82RXzUmoWd7lKh.png)