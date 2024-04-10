---
layout: post
title: InternLM2 Lesson 2 - 轻松玩转书生·浦语趣味 Demo
---

讲师：任宇鹏|书生·浦语社区角色扮演 SIG 小组长
[视频地址](https://www.bilibili.com/video/BV1AH4y1H78d/) 
[文档](https://github.com/InternLM/Tutorial/blob/camp2/helloworld/hello_world.md)

本节要做四个demo，笔记主要侧重在自己不熟悉的环境和命令
## 配置基础环境
```
studio-conda -o internlm-base -t demo
```

用`command -v studio-conda`查找并显示`studio-conda`命令的路径为`alias studio-conda='/share/install_conda_env.sh'`，查看文件内容发现是实验室为方便学员配置环境而wrap的一个命令。以上命令快速将预设环境`internlm-base`拷贝到`demo`。

`conda activate demo`激活环境，之后安装必要的python包，然后从modelscope下载`internlm2-chat-1.8b`模型。

## 命令行运行模型
运行以下命令启动模型
```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM


model_name_or_path = "/root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b"

tokenizer = AutoTokenizer.from_pretrained(model_name_or_path, trust_remote_code=True, device_map='cuda:0')
model = AutoModelForCausalLM.from_pretrained(model_name_or_path, trust_remote_code=True, torch_dtype=torch.bfloat16, device_map='cuda:0')
model = model.eval()

system_prompt = """You are an AI assistant whose name is InternLM (书生·浦语).
- InternLM (书生·浦语) is a conversational language model that is developed by Shanghai AI Laboratory (上海人工智能实验室). It is designed to be helpful, honest, and harmless.
- InternLM (书生·浦语) can understand and communicate fluently in the language chosen by the user such as English and 中文.
"""

messages = [(system_prompt, '')]

print("=============Welcome to InternLM chatbot, type 'exit' to exit.=============")

while True:
    input_text = input("\nUser  >>> ")
    input_text = input_text.replace(' ', '')
    if input_text == "exit":
        break

    length = 0
    for response, _ in model.stream_chat(tokenizer, input_text, messages):
        if response is not None:
            print(response[length:], flush=True, end="")
            length = len(response)
```

![image.png](https://s2.loli.net/2024/04/10/bwq1aodkATUjF6p.png)

## 运行streamlit应用
```bash
streamlit run /root/Tutorial/helloworld/bajie_chat.py --server.address 127.0.0.1 --server.port 6006
```
本地开启端口转发，其中`-L`参数格式为`local_port:remote_host:remote_port`
```bash
ssh -CNg -L 6007:127.0.0.1:6006 root@ssh.intern-ai.org.cn -p 41863 
```

![image.png](https://s2.loli.net/2024/04/10/6eO9tTrEWCgLjiX.png)

## 运行Lagent应用
Lagent是一个智能体框架，提供了一些典型工具为LLM赋能。特性有
- 流式输出：提供 stream_chat 接口，本地就可演示流式demo
- 接口统一：model、action、agent

![](https://github.com/InternLM/Tutorial/raw/camp2/helloworld/images/Lagent-1.png)

### 安装Lagent框架
```
git clone https://gitee.com/internlm/lagent.git
cd /root/demo/lagent
git checkout 581d9fb8987a5d9b72bb9ebd37a95efd47d479ac
pip install -e . 
```
运行lagent demo
```

```

## 运行浦语灵笔2.0
```bash
python /root/demo/InternLM-XComposer/examples/gradio_demo_composition.py  \
--code_path /root/models/internlm-xcomposer2-7b \
--private \
--num_gpus 1 \
--port 6006
```

![](https://github.com/InternLM/Tutorial/blob/camp2/helloworld/images/img-9.png)

```bash
python /root/demo/InternLM-XComposer/examples/gradio_demo_chat.py  \
--code_path /root/models/internlm-xcomposer2-vl-7b \
--private \
--num_gpus 1 \
--port 6006
```

![](https://github.com/InternLM/Tutorial/raw/camp2/helloworld/images/img-7.png)

