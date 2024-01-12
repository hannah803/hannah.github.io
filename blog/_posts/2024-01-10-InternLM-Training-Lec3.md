---
layout: post
title: InternLM Training - Lec3
subtitle: 参加书生·浦语大模型实战营的笔记，第三课《基于InternLM和LangChain搭建你的知识库》
author: Hannah
categories: InterLM
banner:
  image: https://bit.ly/3xTmdUP
  opacity: 1
  background: "#000"
  height: "50vh" 
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: none"
  subheading_style: "color: 0xffffff; font-weight: bold"
tags: llm
top: 1
sidebar: []
---
![](https://framerusercontent.com/images/ZMXHfyFcTURicVBX66x8mRea8.png)

## Lec 3 - 基于 InternLM 和 LangChain 搭建你的知识库

-----------------------------------

prompt-engineering-for-developers 开源项目负责人 邹丽衡

[文档](https://github.com/InternLM/tutorial/blob/main/langchain/readme.md) [视频](https://www.bilibili.com/video/BV1sT4y1p71V/)

### RAG

![](https://framerusercontent.com/images/bB4duYqCMovpSvQEOEU41FhqKqA.png)![](https://framerusercontent.com/images/tU2yWsDKETYCGJw6v9Ha8d0v24.png)![](https://framerusercontent.com/images/epr0X0lxH9JzggVvMNnUIBcWo.png)

### LangChain

LangChain 框架是一个开源工具，通过为各种 LLM 提供通用接口来简化应用程序的开发流程，帮助开发者自由构建 LLM应用。

LangChain 的核心组成模块：

链（Chains）：将组件组合实现端到端应用，通过一个对象封装实现一系列LLM操作

• Eg.检索问答链，覆盖实现了 RAG（检索增强生成）的全部流程

LangChain 提供了检索问答链模版，可以自动实现知识检索、Prompt 嵌入、LLM问答的全部流程

将基于 InternLM 的自定义 LLM 和已构建的向量数据库接入到检索问答链的上游

调用检索问答链，即可实现知识库助手的核心功能

![](https://framerusercontent.com/images/Ckcd6lDLGgJ6UwavYAX1q247Y.png)

### 实验环境：

[InternStudio](https://studio.intern-ai.org.cn/)

info: Ubuntu 20.04, CUDA 11.7, CuDNN8.5-NCCL2.12, conda

resource: A100(1/4)

### 知识库搭建

1.  #### 数据收集
    

opencompass、lmdeploy、xtuner、InternLM-XComposer、lagent、InternLM仓库中所有的 markdown、txt 文件作为示例语料库

2.  #### 加载数据
    

使用 LangChain 提供的 FileLoader 对象来加载文件（语料），得到由文件解析出的纯文本内容，之后引入到 LangChain 框架中构建向量数据库

3.  #### 构建向量数据库
    

第三方库 `nltk（Natural Language Toolkit）`when 使用开源词向量模型构建开源词向量

*   文本分块
    
    *   LangChain 提供了多种[文本分块工具](https://github.com/datawhalechina/prompt-engineering-for-developers/blob/9dbcb48416eb8af9ff9447388838521dc0f9acb0/content/LangChain%20Chat%20with%20Your%20Data/1.%E7%AE%80%E4%BB%8B%20Introduction.md)，e.g.字符串递归分割器
        
*   文本向量化
    
    *   使用开源词向量模型 [Sentence Transformer](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2)
        
        *   开源词向量模型 [Sentence Transformer](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2)，相对轻量、支持中文且效果较好
            
        *   LangChain 提供了引入 HuggingFace 中的模型并进行向量化的接口
            
    *   Chroma 作为向量数据库
        

![](https://framerusercontent.com/images/045u3gwaxTmf2CzQhIYEcAEGo0.png)

### InterLM接入LangChain

基于本地部署的 InternLM，继承 LangChain 的 LLM 类自定义一个 InternLM LLM 子类，从而实现将 InternLM 接入到 LangChain 框架中，以完全一致的方式调用 LangChain 的接口

继承`LangChain.llms.base.LLM` 并重写构造函数和`_call`函数

### 构建检索问答链

LangChain 通过提供检索问答链对象来实现对于 RAG 全流程的封装。

调用LangChain 提供的 `RetrievalQA` 对象，在初始化时传入数据库和自定义 LLM 作为参数

### 部署

基于 Gradio 框架部署到 Web 网页

![](https://framerusercontent.com/images/BDaY37CADd6RPDiYU1qmucYS7rk.png)

### 作业

【基础】复现课程知识库助手搭建过程 (截图)

![](https://framerusercontent.com/images/vUcqZcZLPvmMq9E9lCaaLCWCv0.png)![](https://framerusercontent.com/images/Fs404taFwZYNQPLriyFoz82Dsk.png)![](https://framerusercontent.com/images/hOgeyiNtnx02h9W3lbvYwIRvfXM.png)

【进阶】选择一个垂直领域，收集该领域的专业资料构建专业知识库，并搭建专业问答助手，并在 [OpenXLab](https://openxlab.org.cn/apps) 上成功部署（截图，并提供应用地址）