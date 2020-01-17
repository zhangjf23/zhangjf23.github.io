---
layout: post
title: NLP中的Subword算法及其实现
categories: NLP
description: Subword算法，BPE， WordPiece
keywords: BPE， WordPiece
---

最近在做机器翻译相关的工作，发现subword算法在NLP各大任务中无处不在。既然要用到并且用好subword，这里就重点捋一遍关于subword的算法以及几个开源的实现。

# 1.word、subword和character
在神经机器翻译中，通常有一个固定的词表，并且模型的训练和预测都非常依赖这个词表。在神经网络的训练过程中，需要对词表中每个词做向量表，每个词对应不同的向量，即embedding的过程。每个不同的词对应不同的向量，即使两个词看起来十分相近，但在训练过程词向量没有任何关系。这就导致一个单词因为拥有不同的形态产生不同的词，从而产生大词汇量的问题。

机器翻译的词表是定长的，但是需要实际翻译的词汇是开放的(out of vocabulary)。以前的做法是新词汇添加到词典中，但是过大的词典会带来两个问题：

* 稀疏问题: 某些词汇出现的频率很低，得不到充分的训练
* 计算量问题: 词典过大，也就意味着embedding过程的计算量会变大

同时，这种word-level的处理方式并不能通过增大词表真正解决OOV的问题，因为再大的词典不能真正覆盖所有的词汇。

为了处理这个问题，一个思路是将字符当做基本单元，建立character-level模型。character-level模型试图使用26个字母加上一些符号去表示所有的词汇，相比于word-level模型，这种处理方式的粒度变小，其输入长度变长，使得数据更加系数并且难以学习长远程的依赖关系。相关工作可参考[Character-Level Neural Machine Translation](https://arxiv.org/abs/1610.03017)，实验结论是基于字符的模型能更好处理OOV问题，并且能更好学习多语言之间通用的语素。

word—level模型导致严重的OOV，而character-level模型粒度又太小，那么subword-level的处理方式就应运而生。subword将单词划分为更小的单元，比如"older"划分为"old" 和 "er"，而这些单元往往能应用到别的词汇当中。举个例子：
```
训练集的词汇: old older oldest smart smarter smartest
word-level 词典: old older oldest smart smarter smartest 长度为6
subword-level 词典: old smart er est 长度为4
```
将词划分成字词的形式，能够大大降低词典的大小。同时，未知词汇能以subword组合的形式表示出来，也能提升词典的表达能力。


# 2. 3种subword算法

如何将词分解成subword，依据不同的策略，产生了几种主流的方法: [Byte Pair Encoding (BPE)](https://www.aclweb.org/anthology/P16-1162/)、[wordpiece](https://arxiv.org/pdf/1609.08144.pdf) 和 [Unigram Language Model](https://arxiv.org/abs/1804.10959)。值得一提的是，这几种算法的处理流程跟语言学没有太大的关系，单纯是统计学的解决思路。

## 2.1 Byte Pair Encoding

## 2.2 wordpiece

## 2.3 unigram language model

# 3. 2种开源实现

## 3.1 subword-nmt

## 3.2 sentencepiece