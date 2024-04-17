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
### Transformer库运行模型
Transformer库是Huggingface社区推出的用于运行HF模型的官方库。
```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("/root/internlm2-chat-1_8b", trust_remote_code=True)

# Set `torch_dtype=torch.float16` to load model in float16, otherwise it will be loaded as float32 and cause OOM Error.
model = AutoModelForCausalLM.from_pretrained("/root/internlm2-chat-1_8b", torch_dtype=torch.float16, trust_remote_code=True).cuda()
model = model.eval()

inp = "hello"
print("[INPUT]", inp)
response, history = model.chat(tokenizer, inp, history=[])
print("[OUTPUT]", response)

inp = "please provide three suggestions about time management"
print("[INPUT]", inp)
response, history = model.chat(tokenizer, inp, history=history)
print("[OUTPUT]", response)
```

![image.png](https://s2.loli.net/2024/04/17/W4JNCSwc7FthkfO.png)

### LMDeploy部署模型
`lmdeploy chat /root/internlm2-chat-1_8b`

![image.png](https://s2.loli.net/2024/04/17/JyPbIQ7uASUrK3n.png)

## LMDeploy量化
常见的 LLM 模型由于 Decoder Only 架构的特性，实际推理时大多数的时间都消耗在了逐 Token 生成阶段（Decoding 阶段），是典型的访存密集型场景。

KV8量化是指将逐 Token（Decoding）生成过程中的上下文 K 和 V 中间结果进行 INT8 量化（计算时再反量化），以降低生成过程中的显存占用。

W4A16 量化，将 FP16 的模型权重量化为 INT4，Kernel 计算时，访存量直接降为 FP16 模型的 1/4，大幅降低了访存成本。Weight Only 是指仅量化权重，数值计算依然采用 FP16（需要将 INT4 权重反量化）。

### KV Cache
KV Cache是一种缓存技术，通过存储键值对的形式来复用计算结果，以达到提高性能和降低内存消耗的目的。在大规模训练和推理中，KV Cache可以显著减少重复计算量，从而提升模型的推理速度。理想情况下，KV Cache全部存储于显存，以加快访存速度。当显存空间不足时，也可以将KV Cache放在内存，通过缓存管理器控制将当前需要使用的数据放入显存。

模型在运行时，占用的显存可大致分为三部分：模型参数本身占用的显存、KV Cache占用的显存，以及中间运算结果占用的显存。LMDeploy的KV Cache管理器可以通过设置`--cache-max-entry-count`参数，控制KV缓存占用剩余显存的最大比例。默认的比例为0.8。

默认情况下
![image.png](https://s2.loli.net/2024/04/17/6XqIo2nBOVayLJN.png)

`lmdeploy chat /root/internlm2-chat-1_8b --cache-max-entry-count 0.5`时
![image.png](https://s2.loli.net/2024/04/17/Gvo4h7iqzmc8Jyu.png)

`lmdeploy chat /root/internlm2-chat-1_8b --cache-max-entry-count 0.01`时
![image.png](https://s2.loli.net/2024/04/17/XmVJArtLjyxz5fQ.png)

### W4A16量化
LMDeploy使用AWQ算法，实现模型4bit权重量化。推理引擎TurboMind提供了非常高效的4bit推理cuda kernel，性能是FP16的2.4倍以上。

```bash
lmdeploy lite auto_awq \
   /root/internlm2-chat-1_8b \
  --calib-dataset 'ptb' \
  --calib-samples 128 \
  --calib-seqlen 1024 \
  --w-bits 4 \
  --w-group-size 128 \
  --work-dir /root/internlm2-chat-1_8b-4bit
```

运行量化模型`lmdeploy chat /root/internlm2-chat-1_8b-4bit --model-format awq`
![image.png](https://s2.loli.net/2024/04/17/72lneMDsVg3JWbz.png)

调整KV cache`lmdeploy chat /root/internlm2-chat-1_8b-4bit --model-format awq --cache-max-entry-count 0.01`时
![image.png](https://s2.loli.net/2024/04/17/CqJ4doTsFHyP7Yc.png)

### LMDeploy Serve
![](https://github.com/InternLM/Tutorial/blob/camp2/lmdeploy/imgs/4_1.jpg)
#### API服务器
```bash
lmdeploy serve api_server \
    /root/internlm2-chat-1_8b \
    --model-format hf \
    --quant-policy 0 \
    --server-name 0.0.0.0 \
    --server-port 23333 \
    --tp 1
ssh -C -N -g -L 6007:127.0.0.1:23333 root@ssh.intern-ai.org.cn -p 41863
```
![image.png](https://s2.loli.net/2024/04/17/CNBQLOGejZkKdR2.png)

##### 命令行API客户端连接API服务器
```bash
lmdeploy serve api_client http://localhost:23333
```
![image.png](https://s2.loli.net/2024/04/17/R4zMDCPonTtqOjH.png)

##### 网页客户端连接API服务器
使用Gradio作为前端，启动网页客户端。
```bash
lmdeploy serve gradio http://localhost:23333 \
    --server-name 0.0.0.0 \
    --server-port 6006
```
![image.png](https://s2.loli.net/2024/04/17/4DOTPSX6f578tqy.png)

#### Python代码集成
##### 运行模型
```python
from lmdeploy import pipeline

pipe = pipeline('/root/internlm2-chat-1_8b')
response = pipe(['Hi, pls intro yourself', '上海是'])
print(response)
```

##### 向TurboMind后端传递参数
通过创建`TurbomindEngineConfig`，向lmdeploy传递参数。

```python
from lmdeploy import pipeline, TurbomindEngineConfig

# 调低 k/v cache内存占比调整为总显存的 20%
backend_config = TurbomindEngineConfig(cache_max_entry_count=0.2)

pipe = pipeline('/root/internlm2-chat-1_8b',
                backend_config=backend_config)
response = pipe(['Hi, pls intro yourself', '上海是'])
print(response)
```

##### 运行结果对比
![image.png](https://s2.loli.net/2024/04/17/W2tFTEyikVGIp48.png)


## 使用LMDeploy运行视觉多模态大模型llava
安装llava依赖库
`pip install git+https://github.com/haotian-liu/LLaVA.git@4e2277a060da264c4f21b364c867cc622c945874`

```python
from lmdeploy.vl import load_image
from lmdeploy import pipeline, TurbomindEngineConfig


backend_config = TurbomindEngineConfig(session_len=8192) # 图片分辨率较高时请调高session_len
# pipe = pipeline('liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config) 非开发机运行此命令
pipe = pipeline('/share/new_models/liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config)

image = load_image('https://raw.githubusercontent.com/open-mmlab/mmdeploy/main/tests/data/tiger.jpeg')
response = pipe(('describe this image', image))
print(response)
```
![image.png](https://s2.loli.net/2024/04/17/4jabmLUiD2f3rNu.png)

### 通过gradio运行llava模型
```python
import gradio as gr
from lmdeploy import pipeline, TurbomindEngineConfig


backend_config = TurbomindEngineConfig(session_len=8192) # 图片分辨率较高时请调高session_len
# pipe = pipeline('liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config) 非开发机运行此命令
pipe = pipeline('/share/new_models/liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config)

def model(image, text):
    if image is None:
        return [(text, "请上传一张图片。")]
    else:
        response = pipe((text, image)).text
        return [(text, response)]

demo = gr.Interface(fn=model, inputs=[gr.Image(type="pil"), gr.Textbox()], outputs=gr.Chatbot())
demo.launch()   
```

![image.png](https://s2.loli.net/2024/04/17/ZhOCK2j4NdkYWST.png)

### 定量比较LMDeploy与Transformer库的推理速度差异
速度测试脚本，word和token在数量上可以近似认为成线性关系。

```python
import torch
import datetime
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("/root/internlm2-chat-1_8b", trust_remote_code=True)

# Set `torch_dtype=torch.float16` to load model in float16, otherwise it will be loaded as float32 and cause OOM Error.
model = AutoModelForCausalLM.from_pretrained("/root/internlm2-chat-1_8b", torch_dtype=torch.float16, trust_remote_code=True).cuda()
model = model.eval()

# warmup
inp = "hello"
for i in range(5):
    print("Warm up...[{}/5]".format(i+1))
    response, history = model.chat(tokenizer, inp, history=[])

# test speed
inp = "请介绍一下你自己。"
times = 10
total_words = 0
start_time = datetime.datetime.now()
for i in range(times):
    response, history = model.chat(tokenizer, inp, history=history)
    total_words += len(response)
end_time = datetime.datetime.now()

delta_time = end_time - start_time
delta_time = delta_time.seconds + delta_time.microseconds / 1000000.0
speed = total_words / delta_time
print("Speed: {:.3f} words/s".format(speed))
```

```python
import datetime
from lmdeploy import pipeline

pipe = pipeline('/root/internlm2-chat-1_8b')

# warmup
inp = "hello"
for i in range(5):
    print("Warm up...[{}/5]".format(i+1))
    response = pipe([inp])

# test speed
inp = "请介绍一下你自己。"
times = 10
total_words = 0
start_time = datetime.datetime.now()
for i in range(times):
    response = pipe([inp])
    total_words += len(response[0].text)
end_time = datetime.datetime.now()

delta_time = end_time - start_time
delta_time = delta_time.seconds + delta_time.microseconds / 1000000.0
speed = total_words / delta_time
print("Speed: {:.3f} words/s".format(speed))
```
可以看到速度提升非常大
![image.png](https://s2.loli.net/2024/04/17/G4WDmFH5KY3bNuM.png)