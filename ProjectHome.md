imdict-chinese-analyzer 是 [imdict智能词典](http://www.imdict.net/) 的智能中文分词模块，算法基于隐马尔科夫模型(Hidden Markov Model, HMM)，是中国科学院计算技术研究所的[ictclas](http://ictclas.org/)中文分词程序的重新实现（基于Java），可以直接为lucene搜索引擎提供\*简体中文\*分词支持。

# Apache Lucene的中文分词 #
[Apache Lucene](http://lucene.apache.org)是java实现的一个高效的文本搜索引擎软件，它适用于几乎所有要求全文检索的应用项目，尤其是跨平台应用。对英文的处理已经非常完善，但在中文方面仍然非常欠缺，目前lucene的contribution中，中日韩语言只有一个分析器（cjk analyzer），还是按中日韩文字来逐个切分的，无论从索引效率还是搜索准确度上都很难符合要求。

## 为什么需要智能分词 ##

中文与西方语言最大的区别就在于语句的词汇之间没有明显的分词界限，但是计算机自然语言处理是按词汇来进行分析的，因此中文分词的效果直接影响中文检索和自然语言处理的准确性。例如：
“**我购买了道具和服装**”，如果采用比较简单的正向最大匹配算法，分词结果是“我 购买 了 道具 **和服** 装”，明显偏离了原句的意思；而采用基于HMM的智能分词模型，能够综合分析句子的结构、各个词的频率以及各个词汇之间的跳转频率，从而能产生最优化的分词结果：“我 购买 了 道具 和 **服装**”

# imdict-chinese-analyzer的特性 #

**1. 完全Unicode支持**

 分词核心模块完全采用Unicode编码，无须各种汉字编码的转换，极大的提升了分词的效率。

**2. 提升搜索效率**

 根据imdict智能词典的实践，在有智能中文分词的情况下，索引文件比没有中文分词的索引文件小1/3

**3. 提高搜索准确度**

 imdict-chinese-analyzer采用了HHMM分词模型，极大的提高了分词的准确率，在此基础上的搜索，比对汉字逐个切分要准确得多！

**4. 更高效的数据结构**

 为了提高效率，针对常用中文检索的应用场景，imdict-chinese-analyzer对一些不必要的功能进行了删减，例如词性标注、人名识别、时间识别等等。另外还修改了算法的数据结构，在内存占用量缩减到1/3的情况下把效率提升了数倍。

## 三种智能分词软件效率对比 ##

ICTCLAS 1.0是中科院张华平博士开发的一款基于HHMM的智能分词软件，最新版本为3.0(商业版)，而imdict-chinese-analyzer 和 ictclas4j 都是基于同一模型开发的Java版分词软件，三者的分词效率对比如下图：

![http://imdict-chinese-analyzer.googlecode.com/files/imdict_compare.png](http://imdict-chinese-analyzer.googlecode.com/files/imdict_compare.png)

| | **ictclas4j** | **imdict-chinese-analyzer** | **ICTCLAS 3.0** |
|:|:--------------|:----------------------------|:----------------|
| 分词速度(字节/秒) |　13.86　        |　**483.64**　                 |　678.15　         |
| 分词速度(汉字/秒)　|　7439　         |　**259517**　                 |　363888　         |

测试数据采用中文文件大小为62390KB，内容长度为33477693字符，各软件独立分词并将分开的词写到文件里。

测试环境为：酷睿E7200双核，内存2G，JDK参数为："-Xms512m -Xmx1024m "

由此对比可以看出， imdict-chinese-analyzer的分词效率与C＋＋实现的ICTCLAS 3.0的分词效率在同一个数量级，是 ictclas4j 的 **36** 倍！