# jieba分词源码阅读

### jieba是python的一个分词库，主要用于对中文的分词

## 特点

#### 一、支持三种分词模式：
    
   * 精确模式，试图将句子最精确地切开，适合文本分析；
   * 全模式，把句子中所有的可以成词的词语都扫描出来, 速度非常快，但是不能解决歧义；
   * 搜索引擎模式，在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词。
   * 支持繁体分词


```python
import jieba
seg_list=jieba.cut("我来到北京清华大学",cut_all=True)#全模式
print("Full Mode:"+"/".join(seg_list))

seg_list=jieba.cut("我来到北京清华大学",cut_all=False)#精确模式
print("Default Mode:"+"/".join(seg_list))

seg_list=jieba.cut("他来到了网易杭研大厦")#默认是精确模式
print("，".join(seg_list))

seg_list=jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")#搜索引擎模式
print("，".join(seg_list))
```

    Building prefix dict from the default dictionary ...
    Dumping model to file cache C:\Users\Yvonne\AppData\Local\Temp\jieba.cache
    Loading model cost 2.173 seconds.
    Prefix dict has been built succesfully.
    

    Full Mode:我/来到/北京/清华/清华大学/华大/大学
    Default Mode:我/来到/北京/清华大学
    他，来到，了，网易，杭研，大厦
    小明，硕士，毕业，于，中国，科学，学院，科学院，中国科学院，计算，计算所，，，后，在，日本，京都，大学，日本京都大学，深造
    

#### 二、支持自定义词典
   * 开发者可以自定义的词典，以包含 jieba 词库里没有的词。自行添加新词可以保证比jiaba识别新词更高的正确率
   * 用法： jieba.load_userdict(file_name) # file_name 为文件类对象或自定义词典的路径
   * 词典格式和 dict.txt 一样，一个词占一行；每一行分三部分：词语、词频（可省略）、词性（可省略），用空格隔开，顺序不可颠倒。         file_name 若为路径或二进制方式打开的文件，则文件必须为 UTF-8 编码。
   * 词频省略时使用自动计算的能保证分出该词的词频。
   * 更改分词器（默认为 jieba.dt）的 tmp_dir 和 cache_file 属性，可分别指定缓存文件所在的文件夹及其文件名，用于受限的文件系统
   
#### 这些都是枯燥的概念，看了之后很难在脑海中搞明白是怎么回事，于是我们做一下尝试，使这些概念能够变得真实一点……
#### 例如：
![Alt text](Desktop/picture.jpg)
   #### 以上这个就是一个自定义词典，词典里的词是我们自定义的，_请注意字典的格式！！！_这就是我们上面第三条概念所强调的内容。
   #### 下面我们就用这个词典来帮助理解自定义词典的用法。
   #### 我们将这个词典存为_userdict.txt_放在了_C:\Users\Yvonne_路径下
   #### 然后我们执行一下的代码：


```python
#encoding=utf-8
from __future__ import print_function, unicode_literals     #从_future_模块中引入了 print_function 和 unicode_literals两个特性。
                                                            #print_function 新的print是一个函数，如果导入此特性，之前的print语句就不能用了。
                                                            #unicode_literals 这个是对字符串使用unicode字符
import sys   #引入 sys 模块
sys.path.append("../")    #sys.path用于获取
import jieba
jieba.load_userdict("userdict.txt")     #加载用户自定义词典
import jieba.posseg as pseg

jieba.add_word('石墨烯')   #在词典中补充词汇
jieba.add_word('凱特琳')
jieba.del_word('自定义词')

test_sent = (
"李小福是创新办主任也是云计算方面的专家\n"
"什么是八一双鹿\n"
"例如我输入一个带“韩玉赏鉴”的标题，在自定义词库中也增加了此词为N类\n"
"「台中」正確應該不會被切開\n"
"mac上可分出「石墨烯」\n"
"此時又可以分出來凱特琳了"
)
words = jieba.cut(test_sent)
print('/'.join(words))

print("="*40)

result = pseg.cut(test_sent)

for w in result:
    print(w.word, "/", w.flag, ", ", end=' ')

print("\n" + "="*40)

terms = jieba.cut('easy_install is great')
print('/'.join(terms))
terms = jieba.cut('python 的正则表达式是好用的')
print('/'.join(terms))

print("="*40)
```

    李小福/是/创新办/主任/也/是/云计算/方面/的/专家/
    /什么/是/八一双鹿/
    /例如/我/输入/一个/带/“/韩玉赏鉴/”/的/标题/，/在/自定义/词库/中/也/增加/了/此/词为/N/类/
    /「/台中/」/正確/應該/不會/被/切開/
    /mac/上/可/分出/「/石墨烯/」/
    /此時/又/可以/分出/來/凱特琳/了
    ========================================
    李小福 / nr ,  是 / v ,  创新办 / i ,  主任 / b ,  也 / d ,  是 / v ,  云计算 / x ,  方面 / n ,  的 / uj ,  专家 / n ,  
     / x ,  什么 / r ,  是 / v ,  八一双鹿 / nz ,  
     / x ,  例如 / v ,  我 / r ,  输入 / v ,  一个 / m ,  带 / v ,  “ / x ,  韩玉赏鉴 / nz ,  ” / x ,  的 / uj ,  标题 / n ,  ， / x ,  在 / p ,  自定义 / l ,  词库 / n ,  中 / f ,  也 / d ,  增加 / v ,  了 / ul ,  此 / r ,  词 / n ,  为 / p ,  N / eng ,  类 / q ,  
     / x ,  「 / x ,  台中 / s ,  」 / x ,  正確 / ad ,  應該 / v ,  不 / d ,  會 / v ,  被 / p ,  切開 / ad ,  
     / x ,  mac / eng ,  上 / f ,  可 / v ,  分出 / v ,  「 / x ,  石墨烯 / x ,  」 / x ,  
     / x ,  此時 / c ,  又 / d ,  可以 / c ,  分出 / v ,  來 / zg ,  凱特琳 / nz ,  了 / ul ,  
    ========================================
    easy_install/ /is/ /great
    python/ /的/正则表达式/是/好用/的
    ========================================
    

### 调整词典

   * 使用 add_word(word, freq=None, tag=None) 和 del_word(word) 可在程序中动态修改词典。(add_word( ) 负责一次性的添加分词字典,del_word( )同理)

   * 使用 suggest_freq(segment, tune=True) 可调节单个词语的词频，使其能（或不能）被分出来。                                     
     举个例子：jieba.suggest_freq(('中','将'),tune=True) #True表示希望分出来，False表示不希望分出来。

   * 注意：自动计算的词频在使用 HMM 新词发现功能时可能无效。


```python
testlist = [
('今天天气不错', ('今天', '天气')),
('如果放到post中将出错。', ('中', '将')),
('我们中出了一个叛徒', ('中', '出')),
]

for sent, seg in testlist:
    print('/'.join(jieba.cut(sent, HMM=False)))
    word = ''.join(seg)
    print('%s Before: %s, After: %s' % (word, jieba.get_FREQ(word), jieba.suggest_freq(seg, True))) 
    print("-"*40)
```

    今天/天气/不错
    今天天气 Before: 0, After: 0
    ----------------------------------------
    如果/放到/post/中/将/出错/。
    中将 Before: 494, After: 494
    ----------------------------------------
    我们/中/出/了/一个/叛徒
    中出 Before: 3, After: 3
    ----------------------------------------
    

   #### 三、MIT 授权协议
    
## 算法

   * 基于前缀词典实现高效的词图扫描，生成句子中汉字所有可能成词情况所构成的有向无环图 (DAG)
   * 采用了动态规划查找最大概率路径, 找出基于词频的最大切分组合
   * 对于未登录词，采用了基于汉字成词能力的 HMM 模型，使用了 Viterbi 算法
    
   #### 有向无环图（DAG）
   * 示例：
    ![Alt text](/Desktop/DAG.jpg)

## 基于 TF-IDF 算法的关键词抽取

 import jieba.analyse
 
 


```python

```
