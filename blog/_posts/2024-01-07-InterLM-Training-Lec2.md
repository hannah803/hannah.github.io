---
layout: post
title: InternLM Training - Lec2
subtitle: 参加书生·浦语大模型实战营的笔记，第二课《轻松玩转书生·浦语大模型趣味Demo》
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

![](https://framerusercontent.com/images/vS5vELdYgGvUYP81YEMwrxup1cg.png)

## Lec 2 - 轻松玩转书生·浦语大模型趣味Demo
-----------------------

by d2l-ai-solutions-manual 开源项目负责人 宋志学

[文档](https://github.com/InternLM/tutorial/blob/main/helloworld/hello_world.md) [视频](https://www.bilibili.com/video/BV1Ci4y1z72H)

大模型
---

包含超大规模参数（数十亿、数千亿个参数）的神经网络模型 应用：NLP、计算机视觉、语音识别

神经网络结构：Transformer、BERT、GPT

面临挑战：计算资源、训练成本、依赖大规模数据、模型的可解释性

模型下载from Hugging face、openxlab、modelscope

浦语·灵笔 视觉-语言大模型

InternLM-7B，对话模型，支持8k token上下文窗口

Demo1 - InternLM-Chat-7B + InternStudio
---------------------------------------

[InternStudio](https://studio.intern-ai.org.cn/)

info: Ubuntu 20.04, CUDA 11.7, CuDNN8.5-NCCL2.12, conda

resource: A100(1/4)

【基础作业】使用 InternLM-Chat-7B 模型生成 300 字的小故事（需截图）。

![](https://framerusercontent.com/images/L9E2m4Q4ucFAS6OjYgz8EBYXW4.png)

【基础作业】熟悉 hugging face 下载功能，使用 `huggingface_hub` python 包，下载 `InternLM-20B` 的 config.json 文件到本地（需截图下载过程）。

![](https://framerusercontent.com/images/yZP0QxFeZkVvF54xqEyslfKrdlI.png)

Demo2 - Lagent 智能体工具调用
----------------------

InternLM-Chat-7B + InternStudio

info: Ubuntu 20.04, CUDA 11.7, CuDNN8.5-NCCL2.12, conda

resource: A100(1/4)

询问这个问题，大模型无法正确处理2月29日这个信息。

![](https://framerusercontent.com/images/MoOUjgSGO6PRkjWRPI0ZLoO70cI.png)

Demo3 - 浦语·灵笔图文理解创作
-------------------

InternLM-xcomposer-7B + InternStudio

info: Ubuntu 20.04, CUDA 11.7, CuDNN8.5-NCCL2.12, conda

resource: A100(2/4)

![](https://framerusercontent.com/images/mvDULTflq02f7qtqtYiM53QQZI.png)

对于图片中文字的识别似乎还有问题，里面包含书籍和演讲者照片应该是从“读书·演讲”的文字发散开来的，还有二维码大概是从“海报”这个结论发散出来的。

![](https://framerusercontent.com/images/zoAGSby0EYQCRS2N5SRMYnLxqdY.png)![](https://framerusercontent.com/images/Nk2CpHcWFfEKhMLXabaZ3enaXOE.png)

环境配置
----

1. 端口转发 `ssh -CNg -L 6006:127.0.0.1:6006 root@ssh.intern-ai.org.cn -p 33656`
    

*   \-C enables compression of data during transmission
    
*   \-N tells SSH that no command will be sent once the tunnel is up
    
*   \-g allows remote hosts to connect to forwarded ports.
    
*   \-L 6006:127.0.0.1:6006: This is the local port forwarding option. It specifies that connections to port 6006 on your local machine should be forwarded to port 6006 on the remote server.
    
*   root@ssh.intern-ai.org.cn: the remote server connect to.
    
*   \-p specifies the port number that SSH should use to connect to the remote server.
    

2. Hugging Face镜像源 `export HF_ENDPOINT=https://hf-mirror.com`