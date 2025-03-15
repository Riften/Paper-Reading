# NavGPT-2: Unleashing Navigational Reasoning Capability for Large Vision-Language Models

AIML, University of Adelaide. Gengze Zhou. Qi Wu
https://github.com/GengzeZhou/NavGPT-2/tree/master

Task: 用 LLM 做 Navigation，同时让 LLM 输出一些 reasoning 信息，例如为什么前往特定位置。

action space 是离散的 graph node。

## Methodology
技术方案和 NaVid 是类似的，用 Q-Former 来提取 image token，用 LLM 来输出 action。

```
You are navigating in an indoor environment given the instruction:
<INST>{instruction}</INST>;
The navigable locations are listed below: {
"Candidate 1, facing a1 degree, front" : <IMG>{image_tokens}</IMG>;
"Candidate 2, facing a2 degree, right" : <IMG>{image_tokens}</IMG>;
...};
Please choose the next direction.
```

学习目标是 imitation

