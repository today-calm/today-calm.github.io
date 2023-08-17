---
title: "了解 NLP 工具 spaCy"
date: 2023-08-16T22:19:16+08:00
lastmod: 2023-08-16T22:38:13+08:00
categories: ["研究"]
tags: ["spaCy"]
summary: ""
---

[spaCy](https://spacy.io/) 是一个 Python 库，用来做 NLP 分析，类似的库还有 NLTK

[spaCy 101: Everything you need to know](https://spacy.io/usage/spacy-101) 介绍了 spaCy 的基本概念

## 学习资源

[NLP Tutorial Python](https://www.youtube.com/playlist?list=PLeo1K3hjS3uuvuAXhYjV2lMEShq2UYSwX)  
[Natural Language Processing with spaCy & Python - Course for Beginners](https://www.youtube.com/watch?v=dIUTsFT2MeQ)  
[INTRODUCTION TO SPACY 3](http://spacy.pythonhumanities.com/intro.html)

## 使用方法

下面是一个从文本中获取各单词的还原词形（lemma）的例子：

```python
import spacy

# 加载 en_core_web_sm 模型，需要单独下载，约 12 MB
nlp = spacy.load("en_core_web_sm")
doc = nlp("Apple is looking at buying U.K. startup for $1 billion")
for token in doc:
    print(token.lemma_)
```

输出结果：

```txt
Apple
be
look
at
buy
U.K.
startup
for
$
1
billion
```
