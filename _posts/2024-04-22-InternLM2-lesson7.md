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
```csv
id,question,A,B,C,D,answer
0,《新时代爱国主义教育实施纲要》提出，要坚持把____作为鲜明主题,实现中华民族伟大复兴的中国梦,爱国和爱党、爱社会主义,走中国特色社会主义道路,维护祖国统一和民族团结,A
1,社会主义核心价值观是社会主义核心价值体系的 ____,外核 ,保障 ,主体 ,内核, D
2,培育和践行社会主义核心价值观，是推进中国特色社会主义伟大事业、实现中华民族伟大复兴中国梦的____, 主要任务 ,主要目标 ,战略任务 ,战略目标,C
3,由____个字构成的社会主义核心价值观的基本内容，为培育和践行社会主义核心价值观提供了基本遵循,32 ,24 ,20 ,16, B
4,____是中国特色社会主义最本质特征和最大制度优势,党的领导 ,民主集中制 ,社会主义制度 ,人民当家做主, A
5,新闻媒体要发挥传播社会主流价值的____作用,主渠道 ,重要 ,社会 ,文化,A
6,爱国主义教育是全民教育，必须突出教育的____,社会性 ,组织性 ,基础性 ,群众性,D
7, ____是中华民族的民族心、民族魂，是中华民族最重要的精神财富，是中国人民和中华民族维护民族独立和民族尊严的强大精神动力,文化自信 ,集体主义 ,爱国主义 ,民族团结,C
8,____是爱国主义精神最坚定的弘扬者和实践者,工人阶级 ,中国共产党 ,劳动人民 ,基层群众,B
9,广泛开展道德实践活动。以____为重点,国民教育 ,诚信建设,媒体宣传 ,组织领导,A
```

in `ceval_gen_new.py`

```python
ceval_subject_mapping = {
    'chinese_core_socialist_values':['Core Socialist Values', '社会主义核心价值观', 'Social Science'],

}
```
![image.png](https://s2.loli.net/2024/04/24/RM8O69cs3Xm1Iif.png)