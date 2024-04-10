---
layout: post
title: InternLM2 Lesson 3 - "茴香豆":零代码搭建你的 RAG 智能助理
---

讲师：北辰|书生·浦语开源社区贡献者
[视频地址](https://www.bilibili.com/video/BV1QA4m1F7t4/)
[文档](https://github.com/InternLM/Tutorial/blob/camp2/huixiangdou/readme.md)

![image.png](https://s2.loli.net/2024/04/10/glEjcirWQmBChUA.png)

## RAG检索增强生成
RAG 的概念最早是由Meta(Facebook)的 Lewis 等人在 2020 《 Retrieval Augmented Generation for Knowledge-Intensive NLP Tasks 》中提出的。

基本工作方式是：
- 将知识源（如文档或网页）分割成chunk ，编码成向量，并存储在向量数据库中。
- 接收到用户的问题后，将问题也编码成向量，并在向量数据库中找到与之最相关的文档块（ top-k chunks ）。
- 将检索到的文档块与原始问题一起作为提示（ prompt ）输入到 LLM 中，生成最终的回答。

VD向量数据库
- 数据存储：将文本及其他数据通过其他预训练的模型转换为固定长度的向量表示，这些向量能够捕捉文本的语义信息。
- 相似性检索：根据用户的查询向量，使用向量数据库快速找出最相关的向量的过程。通常通过**计算余弦相似度**或**点乘**等相似性度量来完成。检索结果根据相似度得分进行排序，最相关的文档将被用于后续的文本生成。
- 向量数据库优化之向量表示的优化：包括使用更高级的文本编码技术，如句子嵌入或段落嵌入，以及对数据库进行优化以支持大规模向量搜索。

目前有三种范式：
- Naive RAG：即最基本的工作方式，用于简单的问答系统、信息检索。
- Advanced RAG：在检索前后进行增强，用于摘要生成、内容推荐。
    - 检索之前：对用户问题进行路由扩展、重写等处理
    - 检索之后：对检索到的信息进行重排序、总结、融合等处理
- Modular RAG：将基础部分、后续优化技术、功能等模块化，可定制，用于多模态任务、对话系统。

![image.png](https://s2.loli.net/2024/04/10/Zye6GuIgJDUNK24.png)

常见优化方法：
- 针对向量数据库
    - 嵌入优化（Embedding Optimization）：稀疏编码器、密集检索器、多任务
    - 索引优化（Indexing Optimization）：细粒度分割（chunk）、优化索引结构、添加元数据、对齐优化、混合检索等
- 检索前后增强
    - 查询优化（Query Optimization）：查询扩展和转换，使问题更清晰
    - 上下文管理（Context Curation）：重排rerank，上下文选择/压缩（信息提取器、信息压缩器），减少冗余信息
- 针对检索
    - 迭代检索（Iterative Retrieval）：根据初始查询和迄今为止生成的文本进行重复搜索
    - 递归检索（Recursive Retrieval）：迭代细化搜索查询、链式推理（ Chain-of-Thought ）指导检索过程
    - 自适应检索（Adaptive Retrieval）：Flare、Self-RAG；使LLM主动决定检索的最佳时机和内容
- 针对大模型
    - 针对性LLM微调（LLM Fine-tuning）：Retriever Fine—tuning，Generator Fine—tuning，Collaborative Fine—tuning

![image.png](https://s2.loli.net/2024/04/10/5sKriGtBbhAcm18.png)

## RAG vs Fine-tuning

![image.png](https://s2.loli.net/2024/04/10/xiEM4ju5bK3JF9R.png)

## RAG评价框架和基准测试
- 经典评估指标（NLP）：
    - 准确率 (Accuracy)
    - 召回率 (Recall)
    - F1 分数 (F1 Score)
    - BLEU 分数（用于机器翻译和文本生成）
    - ROUGE 分数（用于文本生成的评估）

- 针对RAG的评价点：
    - 对抗噪音的鲁棒性
    - 拒答能力
    - 信息融合能力
    - 真实性
    - 回答的相关度
    - 总结能力
    - 纠错能力

- RAG评测框架：
    - 基准测试 - RGB、RECALL、CRUD
    - 评测工具 - RAGAS、ARES、TruLens

![image.png](https://s2.loli.net/2024/04/10/WUiozf2vLjg5Em7.png)


