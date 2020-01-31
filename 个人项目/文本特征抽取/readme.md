# 期末个人项目——文本特征抽取（mini Coh-Metrix）

## 项目题目：
文本特征抽取：参考Coh-Metrix version3.0 indices，编写自己的mini版Coh-Metrix（中文或英文），只需要实现 Descriptive、Lexical Diversity 和Readability 中的指标，每类指标至少选取三个以上指标进行实现。 输出结果可以参考Cohmatrix-Webtool。验证方法，找任意文章作为输入，输出实现的各指标的数值结果。

## 完成情况：
#### 实现了抽取给定文本的特征：
* 描述性特征（段落数、句子数、单词数、平均句长和平均单词音节长）
* 文本可读性
* 词汇丰富度

|函数 | 功能|
|--------------------------------|------------------------------------------|
|clearBlankLine & TextFetch |获取文本内容，去除文本中空行|
|DESPC & DESSC & DESWC & ASL & ASW |返回文本段落数、句子数、单词数、平均句长和平均单词音节长|
|LDTTRc & LDTTRa & LDMTLDa	|返回文本实词ttr，全文ttr和MTLD词汇丰富度|
|RDFRE & RDFKGL	|返回The Flesch Reading Ease和Flesch-Kincaid Grade Level|

#### 参考链接：
http://141.225.41.245/cohmetrixhome/documentation_indices.html
https://blog.csdn.net/weixin_38008864/article/details/102855031
https://blog.csdn.net/qq_36652619/article/details/77253208
