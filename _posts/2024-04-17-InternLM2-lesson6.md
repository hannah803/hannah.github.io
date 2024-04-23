---
layout: post
title: InternLM2 Lesson 6 - Lagent & AgentLego 智能体应用搭建
---

讲师：樊奇|Lagent AgentLego 贡献者
[视频地址](https://www.bilibili.com/video/BV1Xt4217728/)
[文档](https://github.com/InternLM/Tutorial/blob/camp2/agent/README.md)

## Agent (智能体)
**perception** of dynamic conditions in the environment, **action** to affect conditions in the environment, **reasoning** to interpret perceptions, solve problems, draw inferences, and determine actions.  --by Hayes Roth 1995

### 智能体范式 Paradigm
![image.png](https://s2.loli.net/2024/04/18/XQWApje4a5GPEdg.png)

#### AutoAct
![image.png](https://s2.loli.net/2024/04/18/rZD9cOPRsTSHnwU.png)

#### ReWoo (Reasoning WithOut Observation）
detaches the reasoning process from external observations): Planner, Worker, Solver (DAG)


#### ReAct = Reason + Act

## Lagent & AgentLego
Lagent 是一个轻量级开源智能体框架，旨在让用户可以高效地构建基于大语言模型的智能体。同时它也提供了一些典型工具以增强大语言模型的能力。

AgentLego 是一个提供了多种开源工具 API 的多模态工具包。

###  Lagent：轻量级智能体框架
- lmdeploy启动api server
```bash
lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                            --server-name 127.0.0.1 \
                            --model-name internlm2-chat-7b \
                            --cache-max-entry-count 0.1
```

- 启动Lagent web Demo
```bash
streamlit run internlm2_agent_web_demo.py --server.address 127.0.0.1 --server.port 7860
```
![image.png](https://s2.loli.net/2024/04/22/TElInfGvAW1jxNP.png)

#### 用 Lagent 自定义工具
使用 Lagent 自定义工具主要分为以下几步：
- 继承 BaseAction 类
- 实现简单工具的 run 方法；或者实现工具包内每个子工具的功能
- 简单工具的 run 方法可选被 tool_api 装饰；工具包内每个子工具的功能都需要被 tool_api 装饰

![image.png](https://s2.loli.net/2024/04/22/I1kmtfPCvihH658.png)

#### AgentLego：组装智能体“乐高”
##### Direct Use
```python
import re

import cv2
from agentlego.apis import load_tool

# load tool
tool = load_tool('ObjectDetection', device='cuda')

# apply tool
visualization = tool('/root/IMG_7802.jpg')
print(visualization)

# visualize
image = cv2.imread('/root/agent/7802.jpg')

preds = visualization.split('\n')
pattern = r'(\w+) \((\d+), (\d+), (\d+), (\d+)\), score (\d+)'

for pred in preds:
    name, x1, y1, x2, y2, score = re.match(pattern, pred).groups()
    x1, y1, x2, y2, score = int(x1), int(y1), int(x2), int(y2), int(score)
    cv2.rectangle(image, (x1, y1), (x2, y2), (0, 255, 0), 1)
    cv2.putText(image, f'{name} {score}', (x1, y1), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 0), 1)

cv2.imwrite('/root/agent/road_detection_direct.jpg', image)
```

##### 作为智能体工具使用
修改`/agentlego/webui/modules/agents/lagent_agent.py`文件中模型名字；启动一个 api_server

```bash
lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                            --server-name 127.0.0.1 \
                            --model-name internlm2-chat-7b \
                            --cache-max-entry-count 0.1

python /root/agent/agentlego/webui/one_click.py
```
![image.png](https://s2.loli.net/2024/04/23/CMVpjKvHOtU7Dxh.png)

上传图片
![IMG_7802.JPG](https://s2.loli.net/2024/04/23/nfSqF9eYlOdiLRV.jpg)

![image.png](https://s2.loli.net/2024/04/23/ldj2S3Bhw9Oix7J.png)

##### 用 AgentLego 自定义工具
自定义工具主要分为以下几步：
- 继承 BaseTool 类
- 修改 default_desc 属性（工具功能描述）
- 如有需要，重载 setup 方法（重型模块延迟加载）
- 重载 apply 方法（工具功能实现）
其中第一二四步是必须的步骤。

实现一个调用 MagicMaker 的 API 以实现图像生成的工具
1. 通过 `touch /root/agent/agentlego/agentlego/tools/magicmaker_image_generation.py`新建工具文件
2. 修改 `/root/agent/agentlego/agentlego/tools/__init__.py` 文件，将工具注册在工具列表中
3. `python /root/agent/agentlego/webui/one_click.py`

![image.png](https://s2.loli.net/2024/04/23/vGKeqIEb4gwAo9i.png)
![image.png](https://s2.loli.net/2024/04/23/1FSoR4ye3f9b6Mm.png)
![image.png](https://s2.loli.net/2024/04/23/5r2eF6yYVZaLhSU.png)
![image.png](https://s2.loli.net/2024/04/23/saejdqE2cJMuWSN.png)

所以，到底什么是小尼学尼章

### Agent 工具能力微调
使用 XTuner 来实现 Agent 工具能力的微调

#### OpenAI Function Calling
在 调用 OpenAI 的 API 时，可以描述函数并让模型智能地选择要输出的 JSON 对象，其中包含传递给一个或多个函数的参数。