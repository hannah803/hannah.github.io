---
layout: post
title: InternLM2 Lesson 7 - OpenCompass 大模型评测实战
---

讲师：曹茂松 刘卓鑫|OpenCompass 贡献者
[视频地址](https://www.bilibili.com/video/BV1Pm41127jU)
[文档](https://github.com/InternLM/Tutorial/blob/camp2/opencompass/readme.md)

## OpenCompass
![image.png](https://s2.loli.net/2024/04/23/DdOWfgE1KkjPzCG.png)

### 自研高质量大模型评测基准
![image.png](https://s2.loli.net/2024/04/23/G8cBP4vAUlhaLft.png)
![image.png](https://s2.loli.net/2024/04/23/AxeIDWVhPp6Bus3.png)

### OpenCompass实战
partitioner，分片逻辑，形成分片任务，分发到显卡上
task负责说明每一片任务是什么，由runner提交任务（local or集群），执行逻辑由openicl说明（evaluate、inference、retrieve），summarizer展示结果

```bash
pip install protobuf
python run.py
--datasets ceval_gen \
--hf-path /share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \  # HuggingFace 模型路径
--tokenizer-path /share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \  # HuggingFace tokenizer 路径（如果与模型路径相同，可以省略）
--tokenizer-kwargs padding_side='left' truncation='left' trust_remote_code=True \  # 构建 tokenizer 的参数
--model-kwargs device_map='auto' trust_remote_code=True \  # 构建模型的参数
--max-seq-len 1024 \  # 模型可以接受的最大序列长度
--max-out-len 16 \  # 生成的最大 token 数
--batch-size 2  \  # 批量大小
--num-gpus 1  # 运行模型所需的 GPU 数量
--debug
```

![image.png](https://s2.loli.net/2024/04/23/EcMdZ7qGA9msS4h.png)

#### 自建客观数据集