---
layout: post
title: NLP中的Subword算法
categories: NLP
description: Subword算法，BPE， WordPiece
keywords: BPE， WordPiece 
---

# 1. Subword与传统空格分隔分词的对比
* 传统词表示方法无法很好的处理未知或罕见的词汇（OOV问题）
* 传统词tokenization方法不利于模型学习词缀之前的关系，E.g. 模型学到的“old”, “older”, and “oldest”之间的关系无法泛化到“smart”, “smarter”, and “smartest”。
* Character embedding作为OOV的解决方法粒度太细
* Subword粒度在词与字符之间，能够较好的平衡OOV问题

# 2. Byte Pair Encoding(BPE)
BPE(字节对)编码是NLP中常见的Subword算法，它是一种简单的数据压缩形式。BPE将字符串中最常见的一对连续字节数据被替换为该数据中不存在的字节，后期使用时需要一个替换表来重建原始数据。

BPE的过程可以理解为把一个单词再拆分，使得我们的此表会变得精简，并且寓意更加清晰。比如"loved","loving","loves"这三个单词。其实本身的语义都是“爱”的意思，但是如果我们以单词为单位，那它们就算不一样的词，在英语中不同后缀的词非常的多，就会使得词表变的很大，训练速度变慢，训练的效果也不是太好。BPE算法通过训练，能够把上面的3个单词拆分成"lov","ed","ing","es"几部分，这样可以把词的本身的意思和时态分开，有效的减少了词表的数量

## 2.1 优缺点
* 优点
	* 可以有效地平衡词汇表大小和步数(编码句子所需的token数量)。
* 缺点
	* 基于贪婪和确定的符号替换，不能提供带概率的多个分片结果。

## 2.2 算法
1. 准备足够大的训练语料
2. 确定期望的subword词表大小
3. 将单词拆分为字符序列并在末尾添加后缀“ </ w>”，统计单词频率。 本阶段的subword的粒度是字符。 例如，“ low”的频率为5，那么我们将其改写为“ l o w </ w>”：5
4. 统计每一个连续字节对的出现频率，选择最高频者合并成新的subword
5. 重复第4步直到达到第2步设定的**subword词表大小**或下一个最高频的字节对出现频率为1

每次合并后词表可能出现3种变化：

* +1，表明加入合并后的新字词，同时原来在2个子词还保留（2个字词不是完全同时连续出现）
* +0，表明加入合并后的新字词，同时原来2个子词中一个保留，一个被消解（一个字词完全随着另一个字词的出现而紧跟着出现）
* -1，表明加入合并后的新字词，同时原来2个子词都被消解（2个字词同时连续出现）

实际上，随着合并的次数增加，词表大小通常先增加后减小。

## 2.3 BPE实现

```
import re

def process_raw_words(words, endtag='@@'):
    """把單詞分割成最小的符號，並且加上結尾符號"""
    vocab = {}
    for word, count in words.items():
        # 加上空格
        word = re.sub(r'([a-zA-Z])', r' \1', word)
        word += ' ' + endtag
        vocab[word] = count
    return vocab

def get_symbol_pairs(vocabs):
    """ 獲得詞彙中所有的字符pair，連續長度爲2，並統計出現次數
    Args:
        vocabs: 單詞dict，(word, count)單詞的出現次數。單詞已經分割爲最小的字符
    Returns:
        pairs: ((符號1, 符號2), count)
    """
    # pairs = collections.defaultdict(int)
    pairs = dict()
    for word, freq in vocabs.items():
        # 單詞裏的符號
        symbols = word.split()
        for i in range(len(symbols) - 1):
            p = (symbols[i], symbols[i + 1])
            pairs[p] = pairs.get(p, 0) + freq
    return pairs

def merge_symbols(symbol_pair, vocabs):
    """把vocabs中的所有單詞中的'a b'字符串用'ab'替換
    Args:
        symbol_pair: (a, b) 兩個符號
        vocabs: 用subword(symbol)表示的單詞，(word, count)。其中word使用subword空格分割
    Returns:
        vocabs_new: 替換'a b'爲'ab'的新詞彙表
    """
    vocabs_new = {}
    raw = ' '.join(symbol_pair)
    merged = ''.join(symbol_pair)
    # 非字母和數字字符做轉義
    bigram = re.escape(raw)
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word, count in vocabs.items():
        word_new = p.sub(merged, word)
        vocabs_new[word_new] = count
    return vocabs_new

if __name__ == '__main__':
    raw_words = {"low": 5, "lower": 2, "newest": 6, "widest": 3}
    vocabs = process_raw_words(raw_words)
    print('分成字符并添加终止符：', vocabs)
    num_merges = 10
    for i in range(1, num_merges+1):
        print('round %d:' % i)
        pairs = get_symbol_pairs(vocabs)
        print('组合并统计频数：', pairs)
        symbol_pair = max(pairs, key=pairs.get)
        print('频数最大的组合:', symbol_pair)
        vocabs = merge_symbols(symbol_pair, vocabs)
        print('更新后:', vocabs)
```

**结果**

```
分成字符并添加终止符： {' l o w @@': 5, ' l o w e r @@': 2, ' n e w e s t @@': 6, ' w i d e s t @@': 3}
round 1:
组合并统计频数： {('l', 'o'): 7, ('o', 'w'): 7, ('w', '@@'): 5, ('w', 'e'): 8, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('e', 's'): 9, ('s', 't'): 9, ('t', '@@'): 9, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'e'): 3}
频数最大的组合: ('e', 's')
更新后: {' l o w @@': 5, ' l o w e r @@': 2, ' n e w es t @@': 6, ' w i d es t @@': 3}
round 2:
组合并统计频数： {('l', 'o'): 7, ('o', 'w'): 7, ('w', '@@'): 5, ('w', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('w', 'es'): 6, ('es', 't'): 9, ('t', '@@'): 9, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'es'): 3}
频数最大的组合: ('es', 't')
更新后: {' l o w @@': 5, ' l o w e r @@': 2, ' n e w est @@': 6, ' w i d est @@': 3}
round 3:
组合并统计频数： {('l', 'o'): 7, ('o', 'w'): 7, ('w', '@@'): 5, ('w', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('w', 'est'): 6, ('est', '@@'): 9, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est'): 3}
频数最大的组合: ('est', '@@')
更新后: {' l o w @@': 5, ' l o w e r @@': 2, ' n e w est@@': 6, ' w i d est@@': 3}
round 4:
组合并统计频数： {('l', 'o'): 7, ('o', 'w'): 7, ('w', '@@'): 5, ('w', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('w', 'est@@'): 6, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('l', 'o')
更新后: {' lo w @@': 5, ' lo w e r @@': 2, ' n e w est@@': 6, ' w i d est@@': 3}
round 5:
组合并统计频数： {('lo', 'w'): 7, ('w', '@@'): 5, ('w', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('w', 'est@@'): 6, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('lo', 'w')
更新后: {' low @@': 5, ' low e r @@': 2, ' n e w est@@': 6, ' w i d est@@': 3}
round 6:
组合并统计频数： {('low', '@@'): 5, ('low', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('n', 'e'): 6, ('e', 'w'): 6, ('w', 'est@@'): 6, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('n', 'e')
更新后: {' low @@': 5, ' low e r @@': 2, ' ne w est@@': 6, ' w i d est@@': 3}
round 7:
组合并统计频数： {('low', '@@'): 5, ('low', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('ne', 'w'): 6, ('w', 'est@@'): 6, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('ne', 'w')
更新后: {' low @@': 5, ' low e r @@': 2, ' new est@@': 6, ' w i d est@@': 3}
round 8:
组合并统计频数： {('low', '@@'): 5, ('low', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('new', 'est@@'): 6, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('new', 'est@@')
更新后: {' low @@': 5, ' low e r @@': 2, ' newest@@': 6, ' w i d est@@': 3}
round 9:
组合并统计频数： {('low', '@@'): 5, ('low', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('low', '@@')
更新后: {' low@@': 5, ' low e r @@': 2, ' newest@@': 6, ' w i d est@@': 3}
round 10:
组合并统计频数： {('low', 'e'): 2, ('e', 'r'): 2, ('r', '@@'): 2, ('w', 'i'): 3, ('i', 'd'): 3, ('d', 'est@@'): 3}
频数最大的组合: ('w', 'i')
更新后: {' low@@': 5, ' low e r @@': 2, ' newest@@': 6, ' wi d est@@': 3}
```

## 2.4 编码和解码
### 2.4.1 编码
在之前的算法中，我们已经得到了subword的词表，对该词表按照子词长度由大到小排序。编码时，对于每个单词，遍历排好序的字词词表寻找是否有token是当前单词的子字符串，如果有，则该token是表示单词的tokens之一。

我们从最长的token迭代到最短的token，尝试将每个单词中的子字符串替换为token。 最终，我们将迭代所有tokens，并将所有子字符串替换为tokens。 如果仍然有子字符串没被替换但所有token都已迭代完毕，则将剩余的子词替换为特殊token，如`<unk>`。

```
# 给定单词序列
[“the</w>”, “highest</w>”, “mountain</w>”]

# 假设已有排好序的subword词表
[“errrr</w>”, “tain</w>”, “moun”, “est</w>”, “high”, “the</w>”, “a</w>”]

# 迭代结果
"the</w>" -> ["the</w>"]
"highest</w>" -> ["high", "est</w>"]
"mountain</w>" -> ["moun", "tain</w>"]
```

### 2.4.2 解码
将所有的tokens拼接在一起，如

```
# 编码序列
[“the</w>”, “high”, “est</w>”, “moun”, “tain</w>”]

# 解码序列
“the</w> highest</w> mountain</w>”
```

## 2.5 FastBPE
效率优化的BPE算法：[FastBPE](https://github.com/glample/fastBPE)

# 3. 参考文献
1. 参考论文：[Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/pdf/1508.07909.pdf)
2. 相关讲解：[Subword策略](https://plmsmile.github.io/2017/10/19/subword-units/)

