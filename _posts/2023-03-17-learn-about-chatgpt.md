---
title: ChatGPT相关调研
date: 2023-03-17 12:00:00
category: 技术
tags: [AI]
---

本文是对[ChatGPT](https://openai.com/blog/chatgpt)的调研，包括ChatGPT是啥，它是怎么来的，之后会怎样。

<!--more-->

## ChatGPT是啥？

### 聊天机器人

普通用户初见的ChatGPT是一个聊天机器人，或者智能助理。它能回答各种问题，并基于此帮人做各种事情。下面是个例子。

![](/images/2023-03-17-chat-example.png)

和Siri不同，它无法解决“今天天气怎样？”甚至“现在几点？”这样的问题。
因为它缺乏2022年及之后的信息，并且不具备实时查询或搜索的能力。

### 应用编程接口（API）

用于实现上述聊天功能的是OpenAI提供的[编程接口](https://platform.openai.com/docs/guides/chat)。

调用示例：

```python
# Note: you need to be using OpenAI Python v0.27.0 for the code below to work
import openai

openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)

```

返回结果：
```python
{
 'id': 'chatcmpl-6p9XYPYSTTRi0xEviKjjilqrWU2Ve',
 'object': 'chat.completion',
 'created': 1677649420,
 'model': 'gpt-3.5-turbo',
 'usage': {'prompt_tokens': 56, 'completion_tokens': 31, 'total_tokens': 87},
 'choices': [
   {
    'message': {
      'role': 'assistant',
      'content': 'The 2020 World Series was played in Arlington, Texas at the Globe Life Field, which was the new home stadium for the Texas Rangers.'},
    'finish_reason': 'stop',
    'index': 0
   }
  ]
}

```

`message`中的任意字符（role、content等）都占用token数。一个token对应一个英文单词，或者（如果单词较长）一个单词中的一部分。
输入加输出的token数量上限为4096，对应英文词数为3000左右；超出的会被截断。
调用时需要包含历史消息才能取得好的效果，尤其当上下文是必要的时候。例如上述消息“Where was it played?”中it的理解就依赖上下文。

另有输入参数`temperature`，用于控制结果的随机性，设为0则每次返回一样，大于0的值可以避免千篇一律。

### 大规模语言模型

所谓语言模型，就是对真实自然语言的数学建模。该模型接收给定的输入文本序列，预测接下来会出现什么字符（给出出现各个字符的概率分布）或字符串。

例如输入：

	As Descartes said, I think, therefore,

模型输出：

	I am.

从而补全了“如笛卡尔所说，我思，故我在。”这一英文句子。

这是语言模型的典型应用方式：输入引导词/提示（prompt），由模型接着做补全（completion）。
如果引导词设计合理，且补全做的很智能，则可以用于解决自然语言处理（NLP）的各项任务。
这里既包含文本理解，也包含文本生成。如何适配特定任务就变成了如何进行“引导词设计”。
“不是AI回答的不好，是我提问的方式不对”就源于此意。

下面是一些NLP任务引导词的[例子](https://platform.openai.com/docs/guides/completion)。

分类：

	Decide whether a Tweet's sentiment is positive, neutral, or negative.
	Tweet: I loved the new Batman movie!
	Sentiment:

翻译：

	Translate this into French, Spanish and Japanese:
	What rooms do you have available?

问答：

	The following is a conversation with an AI assistant. The assistant is helpful, creative, clever, and very friendly.
	Human: Hello, who are you?
	AI: I am an AI created by OpenAI. How can I help you today?
	Human:

对这些引导词内容的成功续写，就是我们想要的任务结果。

ChatGPT本质上就是一个超大规模的语言模型。它对外展示的聊天（chat）功能也是基于强大的“引导词+补全”机制来实现。

大规模语言模型（Large Language Model，LLM）中的“大”体现在训练采用的语料多、模型参数多等方面。ChatGPT属于GPT系列模型中的第3.5代。
该系列从第一代GPT-1开始，规模就在几十成百成倍地增长。如下表所示：

| | 发布时间 | 模型参数量 | 预训练数据大小 |
| :---- | :---- | ----: | ----: |
| GPT-1： | 2018年6月 | 1.17 亿 | 5 GB |
| GPT-2： | 2019年2月 | 15 亿 | 40 GB |
| GPT-3： | 2020年5月 | 1750 亿 | 45 TB |

## ChatGPT怎么来的？

### GPT：预训练+微调，半监督学习

GPT全称为Generative Pre-Training，源于论文[《Improving Language Understanding by Generative Pre-Training》](https://www.cs.ubc.ca/~amuham01/LING530/papers/radford2018improving.pdf)。
其提出将NLP任务的解决方法从监督学习（需要大量标注数据）演进为半监督学习：先从大量无标注语料得到任务无关的预训练模型，然后用任务相关的标注数据做微调（fine-tune）。

论文中的下面两幅图展示了方法的架构和效果。

![](/images/2023-03-17-gpt-fine-tune.png)

![](/images/2023-03-17-gpt-performance.png)

可以看到，GPT采用了[transformer](http://jalammar.github.io/illustrated-transformer/)架构。
该架构中encoder来理解输入，decoder来生成输出，并且通过attention机制能覆盖到更长/更远的上下文信息。


### GPT-2和GPT-3：任务无关模型，无监督学习

GPT-2和GPT-3除了模型规模的增大，在预训练之后不再针对特定任务进行微调，而是直接用无监督训练出来的语言模型，通过“引导词+补全”的方式来完成各项任务。

在GPT-3的论文[《Language Models are Few-Shot Learners》](https://proceedings.neurips.cc/paper/2020/file/1457c0d6bfcb4967418bfb8ac142f64a-Paper.pdf)中，引导词所包含的提示信息又分为了以下三种程度：
* Few-Shot，即在引导词中给出K（10～100，受限于2048的token数量）个例子，模型接着进行补全。
* One-Shot，在引导词中给出1个例子。
* Zero-Shot，不给例子，仅描述任务。

对于Few-Shot的情况，它相当于是把给出的几个例子作为标注数据进行了简单的fine-tune。在OpenAI提供的API中，也支持自行上传更多标注数据进行fine-tune以更好解决特定任务。

GPT-3能够理解任务描述并较好完成，部分表现已经超过监督学习的模型。这得益于它使用的海量预训练语料，包括：
* Common Crawl，从2008年开始十几年的网页抓取。
* WebText，OpenAI研究者选取的高质量网页（在reddit上经过用户点赞3次的链接）。
* Books1和Books2，两个互联网书籍语料库。
* English-language Wikipedia。

### InstructGPT：重新加入监督学习，反映人工偏好

GPT-3的优化目标是补全的文字更符合统计概率，但结果不一定是人想要的。
其改进版[InstructGPT](https://openai.com/research/instruction-following)则通过加入监督学习和人工反馈，使语言模型的输出更符合人的意图。这称为对齐（alignment）。

例如下面的对比中，GPT-3的回答明显像是从一些出题或问答类网站上学到了频繁出现的套话，而不像InstructGPT那样是我们想要的回复。

![](/images/2023-03-17-instructgpt-vs-gpt3-example1.png)

![](/images/2023-03-17-instructgpt-vs-gpt3-example2.png)

InstructGPT的具体训练方法如下图所示（来自论文[《Training language models to follow instructions with human feedback》](https://arxiv.org/abs/2203.02155)）。

![](/images/2023-03-17-instructgpt-method.png)

主要包括三个部分：
1. 人工标注补全输出，形成示例数据集，用来对GPT-3进行有监督的微调（Supervised FineTune，SFT）；
2. 人工标注对不同补全输出的偏好排名，形成对比数据集，用来训练奖励模型（Reword Model，RM）；
3. 使用第2步训练得到的RM作为奖励函数，通过强化学习优化第1步训练得到的SFT模型。

### ChatGPT：增加安全性

[ChatGPT](https://openai.com/blog/chatgpt)和InstructGPT采用了同样的方法，只是数据采集方式稍有不同。
此外，ChatGPT在发布后的持续迭代中，加入了内容过滤等安全机制，减少了有害内容。

![](/images/2023-03-17-chatgpt-security-example.png)

或许，教模型不该说什么，和教它该说什么一样充满挑战性。

## 之后呢？

### 扩展能力

ChatGPT发布以后，它相关的扩展能力也逐渐成熟可用（虽然有些还处于beta版）：

* 文本的插入和修改：这倒是可以通过补全一个正确的最终结果，然后和输入做对比来实现。
* 写代码：[Codex](https://openai.com/research/evaluating-large-language-models-trained-on-code)，加入了GitHub上的代码作为训练数据（编程语言和自然语言可以类似处理）；它是[GitHub Copilot](https://github.com/features/copilot)的基础。
* 音频转文本：[Whisper](https://openai.com/research/whisper)，利用音频和其对应文本作为标注数据，通过监督学习得到的额外模型。
* 图片生成：[DALL-E](https://openai.com/research/dall-e)，将图片处理成像文本一样的1024个token，和图片标题对应的256个token一起作为训练数据。

### GPT-4

近日，ChatGPT的下一代[GPT-4](https://openai.com/research/gpt-4)发布，包括了以下几个新特性。

一、将token上限提升至8192甚至32768（约支持2万多单词），可以处理更复杂的任务了。

二、新增了图片的读取和理解，能解下面这样截图给它的题目。

![](/images/2023-03-17-gpt4-image-input.png)

三、加强了可操纵性，可以指定模型表现为特定“人格”。例如前面API部分有 {"role": "system", "content": "You are a helpful assistant."}这样的系统信息，用于指定该模型为回答问题的助手。
在GPT-4中，该功能得到强化。例如我们可以将系统信息换为"You are a tutor that always responds in the Socratic style. You never give the student the answer, but always try to ask just the right question to help them learn to think for themselves."，来指定模型的回复风格为“苏格拉底式”，也就是不直接回答问题，而只通过反问来引导提问者自己思考解决问题。

看来，AI不仅可以表现得像人，还能像不同的人。
