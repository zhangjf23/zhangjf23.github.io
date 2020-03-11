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

为了处理这个问题，一个思路是将字符当做基本单元，建立character-level模型。character-level模型试图使用26个字母加上一些符号去表示所有的词汇，相比于word-level模型，这种处理方式的粒度变小，其输入长度变长，使得数据更加稀疏并且难以学习长远程的依赖关系。类似的工作可参考[Character-Level Neural Machine Translation](https://arxiv.org/abs/1610.03017)，实验结论是基于字符的模型能更好处理OOV问题，并且能更好学习多语言之间通用的语素。

word-level模型导致严重的OOV，而character-level模型粒度又太小，那么subword-level的处理方式就应运而生。subword将单词划分为更小的单元，比如"older"划分为"old" 和 "er"，而这些单元往往能应用到别的词汇当中。举个例子：
```
训练集的词汇: old older oldest smart smarter smartest
word-level 词典: old older oldest smart smarter smartest 长度为6
subword-level 词典: old smart er est 长度为4
```
将词划分成字词的形式，能够大大降低词典的大小。同时，未知词汇能以subword组合的形式表示出来，也能提升词典的表达能力。


# 2. 3种subword算法

如何将词分解成subword，依据不同的策略，产生了几种主流的方法: [Byte Pair Encoding (BPE)](https://www.aclweb.org/anthology/P16-1162/)、[wordpiece](https://arxiv.org/pdf/1609.08144.pdf) 和 [Unigram Language Model](https://arxiv.org/abs/1804.10959)。值得一提的是，这几种算法的处理流程跟语言学没有太大的关系，单纯是统计学的解决思路。

## 2.1 Byte Pair Encoding

Byte Pair Encoding (BPE) 是一种压缩算法，它属于自下而上的算法。BPE最早应用于NLP任务来源于[Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909)，目前已经是NLP任务中最为简单有效的方法。[GPT-2](https://github.com/openai/gpt-2)中使用BPE作为subword算法，并且WMT比赛中多数提交者使用的subword算法也是BPE。

BPE是一种数据压缩的方式，它将字符串中最常见的一对连续字符数据替换成该字符串中不存在的字符串，后续再通过一个词表重建原始的数据。BPE的处理过程可以理解为一个单词的再拆分过程。如"loved","loving","loves"这三个单词，其本身的语义都是”爱”的意思。BPE通过训练，能够把上面的3个单词拆分成”lov”,”ed”,”ing”,”es”几部分，这样可以把词的本身的意思和时态分开，有效的减少了词表的数量。

BPE算法的流程可参考[论文](https://arxiv.org/abs/1508.07909)，这里添加一些自己的理解。

* 获取subword词表的流程(learn-bpe)
    * 准备语料，分解成最小单元，比如英文中26个字母加上各种符号，作为原始词表
    * 根据语料统计相邻字符对出现的频次
    * 挑出频次最高的相邻字符对，比如"t"和"h"，合并组成"th"，加入词表，训练语料中所有该相邻字符对都进行融合
    * 重复2和3操作，直至词表中单词的数量达到期望，或下一个最高频的字节对出现频率为1

* 编码和解码(apply-bpe及其逆过程)
    * 编码

    得到subword词表后，对该词表按照子词长度由大到小排序。编码时，对于每个单词，遍历排好序的字词词表寻找是否有token是当前单词的子字符串，如果有，则该token是表示单词的tokens之一。从最长的token迭代到最短的token，尝试将每个单词中的子字符串替换为token。 最终，该过程将迭代所有tokens，并将所有子字符串替换为tokens。 如果仍然有子字符串没被替换但所有token都已迭代完毕，则将剩余的子词替换为特殊token，如`<unk>`。
    * 解码

    该过程是编码的逆过程，主要应用在得到翻译输出怎么将subword恢复为word。使用简单的符号替换即可:
    ```
    sed -r 's/(@@ )|(@@ ?$)//g'
    ```

## 2.2 wordpiece

wordpiece作为[Bert](https://arxiv.org/abs/1810.04805)使用的分词方式，其生成词表的方式和BPE非常相近，都是用合并token的方式来生成新的token，最大的区别在于选择合并哪两个token。BPE选择频率最高的相邻字符对进行合并，而wordpiece是基于概率生成的，参考按照作者的原话:
> Choose the new word unit out of all possible ones that increase the likelihood on the training data the most when added to the mode.

从字面上可能有些难以理解，列一下公式就比较清楚了。在做分词的时候假设词和词之间是独立的，所以句子的likelihood等于句子中每个词概率的乘积：

![](https://github.com/zhangjf23/zhangjf23.github.io/blob/master/_posts/pictures/1.jpg)

如果把相邻的x和y两个token合并生成一个新的token叫做z，那么整个句子likelihood的变化可以用下面的式子来表达：

![](https://github.com/zhangjf23/zhangjf23.github.io/blob/master/_posts/pictures/2.jpg)

这不就是两个token之间的互信息嘛！所以wordpiece和BPE的核心区别就在于wordpiece是按token间的互信息来进行合并而BPE是按照token一同出现的频率来合并的。

wordpiece算法中subword词表的学习跟BPE也差不多:

* 准备语料，分解成最小单元，比如英文中26个字母加上各种符号，作为原始词表
* 利用上述语料训练语言模型
* 从所有可能的subword单元中选择加入语言模型后能最大程度地增加训练数据概率的单元作为新的单元
* 重复上步骤，直至词表大小达到设定值或概率增量低于某一阈值

## 2.3 unigram language model

语言模型作为NLP的大厦根基，也是unigram分词的基础。在wordpiece算法中，其实已经用到了language modeling，在选择token进行合并的时候目标就是能提高句子的likelihood。而unigram分词则更进一步，直接以最大化句子的likelihood为目标来直接构建整个词表。

首先，了解一下怎么样在给定词表的条件下最大化句子的likelihood。
给定词表及对应概率值: {"你":0.18, "们":0.16, "好":0.18, "你们":0.15}，对句子”你们好“进行分词:
* 划分为"你@@" "们@@" "好" 的概率为 0.18\*0.16\*0.18=0.005184
* 划分为"你们@@" "好@@" 的概率为 0.15\*0.18=0.027

明显看出后一种分词方式要比前一种好，当然在真实的案例下词表可能有几万个token，直接罗列各种组合的概率显然不可能，所以需要用到Viterbi算法。因此在给定词表的情况下，可以 **1.计算每个token对应的概率；2.找到一个句子最好的分词方式**

但是在词表没有确定的情况下，同时要优化词表和词表里每个token的概率很难做到。unigram分词使用逐步迭代的方式来求解，具体步骤如下：

* 首先初始化一个很大的词表
* 重复以下步骤直到词表数量减少到预先设定的阈值：
    * 保持词表不变，用EM算法来求解每个token的概率
    * 对于每一个token，计算如果把这个token从词表中移除而导致的likelihood减少值，作为这个token的loss
    * 按loss从大到小排序，保留前n%（原文中为80%）的token。

初始化词表可以用不同的方法，一个比较直接的办法就是用所有长度为1的token加上高频出现的ngram来作为起始词表。

# 3. 2种开源实现

> 在自然语言处理中，常见的subword算法开源实现有：
>   * [subword-nmt](https://github.com/rsennrich/subword-nmt)
>   * [fastBPE](https://github.com/glample/fastBPE)
>   * [sentencepiece](https://github.com/google/sentencepiece)
>
## 3.1 subword-nmt

### 3.1.1 简介
subword-nmt的项目地址为: https://github.com/rsennrich/subword-nmt。
文件主要组成部分：
```
.
├── __init__.py
├── apply_bpe.py
├── bpe_toy.py
├── chrF.py
├── get_vocab.py
├── learn_bpe.py
├── learn_joint_bpe_and_vocab.py
├── segment_char_ngrams.py
└── subword_nmt.py
```
subword-nmt可通过pip进行安装
```
pip install subword-nmt
or
pip install https://github.com/rsennrich/subword-nmt/archive/master.zip
```
也可直接```git clone```直接食用：
```
python learn_bpe.py [-options]
```
由于翻译项目动辄几百万的语料，subword-nmt的处理速度要比fastBPE慢很多，此时配合pypy和多进程处理食用更佳。

### 3.1.2. 使用方法

在subword-nmt中，比较关键的脚本有```learn_bpe.py```、```apply_bpe.py```和```get_vocab.py```。一般来说，使用BPE进行分词有以下几个步骤：learn_bpe、apply_bpe和get_vocab。如下:
```
# 学习codes和vocab
cat {train_file}.L1 {train_file}.L2 | subword-nmt learn-bpe -s {num_operations} -o {codes_file}
subword-nmt apply-bpe -c {codes_file} < {train_file}.L1 | subword-nmt get-vocab > {vocab_file}.L1
subword-nmt apply-bpe -c {codes_file} < {train_file}.L2 | subword-nmt get-vocab > {vocab_file}.L2
or
subword-nmt learn-joint-bpe-and-vocab --input {train_file}.L1 {train_file}.L2 -s {num_operations} -o {codes_file} --write-vocabulary {vocab_file}.L1 {vocab_file}.L2

# 对训练语料进行分词
subword-nmt apply-bpe -c {codes_file} --vocabulary {vocab_file}.L1 --vocabulary-threshold 50 < {train_file}.L1 > {train_file}.BPE.L1
subword-nmt apply-bpe -c {codes_file} --vocabulary {vocab_file}.L2 --vocabulary-threshold 50 < {train_file}.L2 > {train_file}.BPE.L2

# 对测试语料进行分词
subword-nmt apply-bpe -c {codes_file} --vocabulary {vocab_file}.L1 --vocabulary-threshold 50 < {test_file}.L1 > {test_file}.BPE.L1

# 得到词典，这里的方法有多种
cat {train_file}.BPE.L1 {train_file}.BPE.L2 | subword-nmt get-vocab > {vocab_file}
or
nematus/data/build_dictionary.py {train_file}.BPE.L1 {train_file}.BPE.L2 # nematus中的实现
```
此外，BPE-dropout是一种为分词过程提供随机性的处理方式。与BPE相比，BPE-dropout在对训练的平行语料进行apply_bpe的过程中，设定一定概率让融合(merge)不通过，这样对于同一个单词，也会出现不同的拆分过程。可以通过调整 ```--dropout 0.1```这个参数为```apply-bpe```这个过程增加随机性。

## 3.2 sentencepiece