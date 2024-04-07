---
layout: post
title: InternLM2 Lesson 1
---

# 书生．浦语大模型全链路开源体系
![image.png](https://s2.loli.net/2024/04/01/GhaMHEPe6tdZkF1.png)

## 回归语言建模的本质
新一代数据清洗过滤技术
- 多维度数据价值评估：基于文本质量、信息质量、信息密度等维度对数据价值进行综合评估与提升
- 高质量语料驱动的数据富集：利用高质量语料的特征从物理世界、互联网以及语料库中进一步富集更多类似语料
- 有针对性的数据补齐针对性补充语料，重点加强世界知识、数理、代码等核心能力

在大规模高质量验证语料上的loss分布左移

## Internlm2主要亮点

![image.png](https://s2.loli.net/2024/04/01/3EVxHkdzb2cuTWY.png)

### GSM8K
Introduced by Cobbe et al. in Training Verifiers to Solve Math Word Problems

GSM8K is a dataset of 8.5K high quality linguistically diverse grade school math word problems created by human problem writers. The dataset is segmented into 7.5K training problems and 1K test problems. These problems take between 2 and 8 steps to solve, and solutions primarily involve performing a sequence of elementary calculations using basic arithmetic operations (+ − ×÷) to reach the final answer. A bright middle school student should be able to solve every problem. It can be used for multi-step mathematical reasoning.

## 从模型到应用
![image-1.png](https://s2.loli.net/2024/04/01/b7Qcy8wFoIae9WE.png)

![image-2.png](https://s2.loli.net/2024/04/01/jc7fCkXNebpt3iV.png)

### 高质量语料
- 书生·万卷1.0: 符合主流中国价值观的中文语料
    - 精细化处理：万卷经过语言筛选、文本提取、格式标准化、数据据过滤和清洗（基于规则和模型）、多尺度去重和数据质量评估等精细数据处理环节，能够很好地适应后续模型训练的要求。
- 书生·万卷CC：安全、信息密度更高的英文语料
![image-3.png](https://s2.loli.net/2024/04/01/V756qB2J8crSpEH.png)


### 预训练框架InternEvo
![image-4.png](https://s2.loli.net/2024/04/01/UBnbERGMSu8Osck.png)

### 微调
- 增量续训
- 有监督微调：全量参数微调、部分参数微调（e.g. Lora）

![image-5.png](https://s2.loli.net/2024/04/01/hQ6DTprFmO1tEKn.png)

![image-6.png](https://s2.loli.net/2024/04/01/lED3qLx1jYPVwUG.png)

### 评测
- 2023.7.6 支持5大能力维度， 70个数据集，40万评测题目
- 发布稳定可复现代码评测镜像，提供多编程语言能力分析和对比
- 发布多模态评测工具套件 VLMEvalKit，支持包括Gemini 、 GPT4V 等商业模型评测
- 目前，100 +评测集 50 万+题目

![image.png](https://s2.loli.net/2024/04/07/2eMzFSHYV9dph58.png)

#### CompassKit
![image.png](https://s2.loli.net/2024/04/07/vYIBKtdrpWchfQX.png)

#### CompassHub
评测基准社区

![image.png](https://s2.loli.net/2024/04/07/Ii7uFG25QyJpbMd.png)

#### 洞见未来
![image.png](https://s2.loli.net/2024/04/07/Nw2xF5IGWtruMis.png)
循环评测策略：把选项轮换给模型做，全部答对才认为答对这一题

![image.png](https://s2.loli.net/2024/04/07/WtyG1lRNP6XCAhm.png)

### 部署
![image.png](https://s2.loli.net/2024/04/07/maOQPlxGwop8RhZ.png)
![image.png](https://s2.loli.net/2024/04/07/6d2siUpjc4mZryz.png)

### 智能体
#### Lagent
- pipeline: ReAct、ReWoo、AutoGPT
![image.png](https://s2.loli.net/2024/04/07/YGDnqHbTgeFvMUs.png)

#### AgentLego
多模态智能体工具箱
- OpenMMLab:计算机视觉领域模型库，高性能算法封装；
- SAM分割模型
![image.png](https://s2.loli.net/2024/04/07/KjlXrtWCL8Ygu5h.png)