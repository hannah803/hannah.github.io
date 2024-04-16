---
layout: post
title: InternLM2 Lesson 5 - LMDeploy 量化部署 LLM 实践
---

讲师：安泓郡|LMDeploy 社区贡献者
[视频地址](https://www.bilibili.com/video/BV1tr421x75B/)
[文档](https://github.com/InternLM/Tutorial/blob/camp2/lmdeploy/README.md)

## 大模型部署面临的挑战
- 计算量巨大，内存开销巨大，访存瓶颈，动态请求
![image.png](https://s2.loli.net/2024/04/16/zB2pvNZg6fMFlS3.png)
![image.png](https://s2.loli.net/2024/04/16/xUYGid8Rjtpfv6J.png)
![image.png](https://s2.loli.net/2024/04/16/KEXnpsNYRhLV2Dc.png)

## 大模型部署方法
### 模型剪枝（Pruning）
剪枝指移除模型中不必要或多余的组件，比如参数，以使模型更加高效。通过对模型中贡献有限的冗余参数进行剪枝，在保证性能最低下降的同时，可以减小存储需求、提高计算效率。

![image.png](https://s2.loli.net/2024/04/16/6PrkRSCXHuBJh4D.png)

### 知识蒸馏（Knowledge Distillation，KD）
通过引导轻量化的学生模型"模仿"性能更好、结构更复杂的教师模型，在不改变学生模型结构的情况下提高其性能。此方法最早用在CV领域。
- 上下文学习（ICL）：ICL distillation
- 思维链（CoT）：MT-COT, Fine-tune-CoT
- 指令跟随 （IF）：LaMini-LM

![image.png](https://s2.loli.net/2024/04/16/hKJTj5IzuHp9xXm.png)

### 模型量化（Quantization）
量化技术将传统的表示方法中的浮点数转换为整数或其他离散形式，以减轻深度学习模型的存储和计算负担。

![image.png](https://s2.loli.net/2024/04/16/ChvzVgylH3kAMZe.png)
![image.png](https://s2.loli.net/2024/04/16/y34HxSQ1MBc5uhm.png)

#### 模型量化如何提升计算效率
仅以定点数存储，计算时还是要反量化为浮点数，但由于访存瓶颈远大于计算瓶颈，通过量化降低访存量，变为原来的1/2甚至1/4，从而提升计算效率。

## LMDeploy
涵盖了 LLM 任务的全套轻量化、部署和服务解决方案。核心功能包括高效推理、可靠量化、便捷服务和有状态推理。
- 高效的推理：
    - **LMDeploy** 开发了 Continuous Batch, Blocked K/V Cache, 动态拆分和融合，张量并行，高效的计算 kernel 等重要特性。
    - TurboMind 是 LMDeploy 团队开发的一款关于 LLM 推理的高效推理引擎，它的主要功能包括： LLaMa 结构模型的支持（针对Llama模型写了一套高度优化的cuda算子实现），continuous batch 推理模式和可扩展的 KV 缓存管理器（前向推理时不用的kv缓存放到内存，显著降低显存占用）。
    - lnternLM2 推理性能是 vLLM 的 1.8 倍。
    - `lmdeploy chat`
- 可靠量化：
    - LMDeploy 支持权重量量化和k/v量化。 
    - W4A16量化（AWQ）：将 FP16 的模型权重量化为INT4, Kernel 计算时，访存量直接降为FP16 模型的1/4，大幅降低了访存成本。Weight Only是指仅量化权重，数值计算依然采用FP16（需要将 INT4 权重反量化）。
    - `lmdeploy lite`
    - 4bit 模型推理效率是 FP16 下的 2.4 倍。
    - 量化模型的可靠性已通过OpenCompass 评测得到充分验证。
- 便捷服务：
    - 通过请求分发服务，LMDeploy支持多模型在多机、多卡上的推理服务。
    - 将 LLM 封装为 HTTP API 服务，支持 Triton 扩展。
    - `lmdeploy serve`
- 有状态推理：
    - 通过缓存多轮对话过程中 Attention 的 k/v ，记住对话历史，从而避免重复处理历史会话。
    - 显著提升长文本多轮对话场景中的效率。
- 视觉大模型推理
    - 对多模态大模型 llava 的支持，使用pipeline便捷运行
    ![image.png](https://s2.loli.net/2024/04/16/MbxlfJhkrY7PTtu.png)

## 实践
Transformer库是Huggingface社区推出的用于运行HF模型的官方库。