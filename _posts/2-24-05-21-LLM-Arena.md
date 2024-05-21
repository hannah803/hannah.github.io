---
layout: post
title: LLM Arena - 中文理解
---


起因是看到了上海人工智能实验室 司南 OpenCompass 和 魔搭 ModelScope 联手推出了大语言模型竞技场 **Compass Arena**的新动态，于是顺着[链接](https://modelscope.cn/studios/opencompass/CompassArena/summary)去体验了一下。
![2024-05-18 21.35.06.png](https://s2.loli.net/2024/05/18/Cn3BmIbT1GaUYxZ.png)
平台分双模型匿名对战和双模型自选对战，先用匿名模式小试一下，脑海里马上想到用弱智吧网友们的智慧来一探大模型的面目，于是凭着记忆问了老鼠药问题，结果还不错，有一个模型直接答对了。
![306b025523603c1d3b97ddedc0325f2.jpg](https://s2.loli.net/2024/05/21/mzkw7OHUc4NuSMo.jpg)

看来弱智吧的问题已经难不住现在的模型了，突然想到前几天有看过一个沙雕网友对话的例子，十分好笑还分享给了朋友，于是就想到用这个问题去试试。
```
A和B在社交软件上对话。
A说：你好，我是来面试的。
B说：明天再来吧，在下大暴雨。
A说：好的，大暴雨叔叔。
```

果然几个模型测下来，答案都不令人满意，主要是没有搞明白“在下”的双关意。
![image.png](https://s2.loli.net/2024/05/21/I2RTHXVyKAJEu1i.png)

然后又去自选模型下面试试，选了目前平台支持的所有模型，都没有答到点上。
![image.png](https://s2.loli.net/2024/05/21/WDS3RrLUYIEFf68.png)
![image.png](https://s2.loli.net/2024/05/21/JrD3GUaN4uwhily.png)
然后转战付费版，试了下gpt-4o，Gemini Pro1.5，以及Claude 3 Sonnet的答案都差不多
![2024-05-18 22.05.05.png](https://s2.loli.net/2024/05/18/mawTyFELW4RHvQo.png)
直到打开了Claude-3 Opus，Oh！！！有希望，于是追问
![image.png](https://s2.loli.net/2024/05/21/kfRmQcoC571FJpb.png)
果然！它是懂的！
![image.png](https://s2.loli.net/2024/05/21/sQ2ILoRE7bZnPkq.png)

好了，我宣布Claude Opus YYDS，以后中文任务就多去找你啦