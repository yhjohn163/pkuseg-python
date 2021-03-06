# pkuseg-python：一个领域细分的中文分词工具包

pkuseg-python简单易用，支持细分领域分词，有效提升了分词准确度。



## 目录

* [主要亮点](#主要亮点)
* [编译和安装](#编译和安装)
* [各类分词工具包的性能对比](#各类分词工具包的性能对比)
* [使用方式](#使用方式)
* [相关论文](#相关论文)
* [作者](#作者)
* [常见问题及解答](#常见问题及解答)



## 主要亮点

pkuseg具有如下几个特点：

1. 多领域分词。不同于以往的通用中文分词工具，此工具包同时致力于为不同领域的数据提供个性化的预训练模型。根据待分词文本的领域特点，用户可以自由地选择不同的模型。 我们目前支持了新闻领域，网络文本领域和混合领域的分词预训练模型，同时也拟在近期推出更多的细领域预训练模型，比如医药、旅游、专利、小说等等。
2. 更高的分词准确率。相比于其他的分词工具包，当使用相同的训练数据和测试数据，pkuseg可以取得更高的分词准确率。
3. 支持用户自训练模型。支持用户使用全新的标注数据进行训练。



## 编译和安装

目前**仅支持python3**，python2支持正在加紧施工。

1. 通过PyPI安装(自带模型文件)：
	```
	pip3 install pkuseg
	之后通过import pkuseg来引用
	```
   **建议更新到最新版本**以获得更好的开箱体验(新版默认提供CTB8的预训练模型、默认关闭词典)：
   	```
	pip3 install -U pkuseg
	```
2. 如果PyPI官方源下载速度不理想，建议使用镜像源，比如：   
   初次安装：
	```
	pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pkuseg
	```
   更新：
	```
	pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple -U pkuseg
	```
3. 从GitHub下载(需要下载模型文件，见[预训练模型](#预训练模型))
	```
	将pkuseg文件放到目录下，通过import pkuseg使用
	模型需要下载或自己训练。
	```
	

## 各类分词工具包的性能对比

我们选择jieba、THULAC等国内代表分词工具包与pkuseg做性能比较。

考虑到jieba分词和THULAC工具包等并没有提供细领域的预训练模型，为了便于比较，我们重新使用它们提供的训练接口在细领域的数据集上进行训练，用训练得到的模型进行中文分词。

我们选择Linux作为测试环境，在新闻数据(MSRA)、混合型文本(CTB8)、网络文本(WEIBO)数据上对不同工具包进行了准确率测试。我们使用了第二届国际汉语分词评测比赛提供的分词评价脚本。其中MSRA与WEIBO使用标准训练集测试集划分，CTB8采用随机划分。对于不同的分词工具包，训练测试数据的划分都是一致的；**即所有的分词工具包都在相同的训练集上训练，在相同的测试集上测试**。对于需要训练的模型，如THULAC和pkuseg，在所有数据集上，我们使用默认的训练超参数。

为了方便用户的使用和比较，我们预训练好的其它工具包的模型可以在[预训练模型](##预训练模型)节下载。



#### 细领域训练及测试结果

以下是在不同数据集上的对比结果：

| MSRA   | Precision | Recall |   F-score |
| :----- | --------: | -----: | --------: |
| jieba  |     87.01 |  89.88 |     88.42 |
| THULAC |     95.60 |  95.91 |     95.71 |
| pkuseg |     96.94 |  96.81 | **96.88** |


| CTB8   | Precision | Recall |   F-score |
| :----- | --------: | -----: | --------: |
| jieba  |     88.63 |  85.71 |     87.14 |
| THULAC |     93.90 |  95.30 |     94.56 |
| pkuseg |     95.99 |  95.39 | **95.69** |

| WEIBO  | Precision | Recall |   F-score |
| :----- | --------: | -----: | --------: |
| jieba  |     87.79 |  87.54 |     87.66 |
| THULAC |     93.40 |  92.40 |     92.87 |
| pkuseg |     93.78 |  94.65 | **94.21** |



#### 跨领域测试结果

我们选用了混合领域的CTB8语料的训练集进行训练，同时在其它领域进行测试，以模拟模型在“黑盒数据”上的分词效果。选择CTB8语料的原因是，CTB8属于混合语料，理想情况下的效果会更好；而且在测试中我们发现在CTB8上训练的模型，所有工具包跨领域测试都可以获得更高的平均效果。以下是跨领域测试的结果：

| CTB8 Training | MSRA  | CTB8  | PKU   | WEIBO | All Average | OOD Average |
| ------------- | ----- | ----- | ----- | ----- | ----------- | ----------- |
| jieba         | 82.75 | 87.14 | 87.12 | 85.68 | 85.67       | 85.18       |
| THULAC        | 83.50 | 94.56 | 89.13 | 91.00 | 89.55       | 87.88       |
| pkuseg        | 83.67 | 95.69 | 89.67 | 91.19 | 90.06       | **88.18**   |

其中，`All Average`显示的是在所有测试集(包括CTB8测试集)上F-score的平均，`OOD Average` (Out-of-domain Average)显示的是在除CTB8外其它测试集结果的平均。



#### 默认模型在不同领域的测试效果

考虑到很多用户在尝试分词工具的时候，大多数时候会使用工具包自带模型测试。为了直接对比“初始”性能，我们也比较了各个工具包的默认模型在不同领域的测试效果(感谢 @yangbisheng2009 的建议)。请注意，这样的比较只是为了说明默认情况下的效果，并不是公平的。

| Default | Training Corpus         | MSRA  | CTB8  | PKU   | WEIBO | All Average | OOD Average |
| ------- | ----------------------- | :---: | :---: | :---: | :---: | :---------: | :---------: |
| jieba   | ?                       | 81.45 | 79.58 | 81.83 | 83.56 | 81.61       | ?           |
| THULAC  | 人民日报(PKU?)|	85.55 | 87.84 | 92.29 | 86.65 | 88.08 | 86.68?      |
| pkuseg  | CTB8                    | 83.67 | 95.69 | 89.67 | 91.19 | **90.06**   | 88.18   |

其中，`All Average`显示的是在所有测试集上F-score的平均，`OOD Average`是去除对应训练语料的测试集后的平均结果。THULAC的介绍中显示默认模型的训练语料为人民日报，同时提供词性标注功能，我们推测其使用的是PKU数据集，如果理解有误，欢迎指正。我们暂时没有发现jieba的默认基于词频模型的语料来源。





## 使用方式

#### 代码示例

以下代码示例适用于python交互式环境。

代码示例1：使用默认配置进行分词，使用CTB8预训练模型，不使用词典。请注意**在pip上发布的0.0.13版本起，接口改为了pkuseg.PKUSeg()，对于使用旧版本的用户请使用pkuseg.pkuseg()进行分词。**
```python3
import pkuseg

seg = pkuseg.PKUSeg()                                  # 以默认配置加载模型
text = seg.cut('我爱北京天安门')                        # 进行分词
print(text)
```

代码示例2：使用默认模型，并使用自定义词典。请留意**凡是在词典中的词一定会单独成词**，因而请仅加入必须切分出来的词。
```python3
import pkuseg

lexicon = ['北京大学', '北京天安门']                     # 希望分词时用户词典中的词固定不分开
seg = pkuseg.PKUSeg(user_dict=lexicon)                  # 加载模型，给定用户词典
text = seg.cut('我爱北京天安门')                         # 进行分词
print(text)
```

代码示例3：使用其它模型，不使用词典
```python3
import pkuseg

seg = pkuseg.PKUSeg(model_name='./ctb8')                # 假设用户已经下载好了ctb8的模型
                                                        # 并放在了'./ctb8'目录下，通过设置model_name加载该模型
text = seg.cut('我爱北京天安门')                         # 进行分词
print(text)
```

代码示例4：对文件分词(使用默认模型，不使用词典)
```python3
import pkuseg

# 对input.txt的文件分词输出到output.txt中
# 使用默认模型，不使用词典，开20个进程
pkuseg.test('input.txt', 'output.txt', nthread=20)     
```


代码示例5：训练新模型
```python3
import pkuseg

# 训练文件为'msr_training.utf8'
# 测试文件为'msr_test_gold.utf8'
# 训练好的模型存到'./models'目录下，开20个进程训练模型
pkuseg.train('msr_training.utf8', 'msr_test_gold.utf8', './models', nthread=20)	
```

#### 多进程

当将以上代码示例置于文件中运行时，如涉及多进程功能，请务必使用`if __name__ == '__main__'`保护全局语句，如：  
mp.py文件
```python3
import pkuseg

if __name__ == '__main__':
    pkuseg.test('input.txt', 'output.txt', nthread=20)
    pkuseg.train('msr_training.utf8', 'msr_test_gold.utf8', './models', nthread=20)	
```
运行
```
python3 mp.py
```
详见[无法使用多进程分词和训练功能，提示RuntimeError和BrokenPipeError](https://github.com/lancopku/pkuseg-python/wiki#3-无法使用多进程分词和训练功能提示runtimeerror和brokenpipeerror)。

**在Windows平台上，请当文件足够大时再使用多进程分词功能**，详见[关于多进程速度问题](https://github.com/lancopku/pkuseg-python/wiki#9-关于多进程速度问题)。

#### 参数说明

模型配置
```
pkuseg.PKUSeg(model_name='ctb8', user_dict=[])
	model_name		模型路径。默认是'ctb8'表示我们预训练好的模型(仅对pip下载的用户)。
	                        用户可以填自己下载或训练的模型所在的路径如model_name='./models'。
	user_dict		设置用户词典。默认不使用词典。填'safe_lexicon'表示我们提供的一个中文词典(仅pip)。
	                        用户可以传入一个包含若干自定义单词的迭代器。
```

对文件进行分词
```
pkuseg.test(readFile, outputFile, model_name='ctb8', user_dict=[], nthread=10)
	readFile		输入文件路径
	outputFile		输出文件路径
	model_name		模型路径。同pkuseg.PKUSeg
	user_dict		设置用户词典。同pkuseg.PKUSeg
	nthread			测试时开的进程数
```

模型训练
```
pkuseg.train(trainFile, testFile, savedir, nthread=10)
	trainFile		训练文件路径
	testFile		测试文件路径
	savedir			训练模型的保存路径
	nthread			训练时开的进程数
```



## 预训练模型

分词模式下，用户需要加载预训练好的模型。我们提供了三种在不同类型数据上训练得到的模型，根据具体需要，用户可以选择不同的预训练模型。以下是对预训练模型的说明：

- MSRA: 在MSRA（新闻语料）上训练的模型。[下载地址](https://pan.baidu.com/s/1twci0QVBeWXUg06dK47tiA)

- CTB8: 在CTB8（新闻文本及网络文本的混合型语料）上训练的模型。随pip包附带的是此模型。[下载地址](https://pan.baidu.com/s/1DCjDOxB0HD2NmP9w1jm8MA)

- WEIBO: 在微博（网络文本语料）上训练的模型。[下载地址](https://pan.baidu.com/s/1QHoK2ahpZnNmX6X7Y9iCgQ)


其中，MSRA数据由[第二届国际汉语分词评测比赛](http://sighan.cs.uchicago.edu/bakeoff2005/)提供，CTB8数据由[LDC](https://catalog.ldc.upenn.edu/ldc2013t21)提供，WEIBO数据由[NLPCC](http://tcci.ccf.org.cn/conference/2016/pages/page05_CFPTasks.html)分词比赛提供。



我们预训练好其它分词软件的模型可以在如下地址下载：

- jieba: 在MSRA、CTB8、WEIBO、PKU语料上的预训练模型，[下载地址](https://pan.baidu.com/s/1F_pQc5UOZ3k9VJYcF9pw3w)，提取码：rnh7
- THULAC: 在MSRA、CTB8、WEIBO、PKU语料上的预训练模型，[下载地址](https://pan.baidu.com/s/11L95ZZtRJdpMYEHNUtPWXA)，提取码：iv82

其中jieba的默认模型为统计模型，主要基于训练数据上的词频信息，我们在不同训练集上重新统计了词频信息。对于THULAC，我们使用其提供的接口进行训练(C++版本)，得到了在不同领域的预训练模型。

欢迎更多用户可以分享自己训练好的细分领域模型。

## 版本历史

- v0.0.11
  - 修订默认配置：CTB8作为默认模型，不使用词典


## 开源协议
1. 本代码采用MIT许可证。
2. 欢迎对该工具包提出任何宝贵意见和建议，请发邮件至jingjingxu@pku.edu.cn。



## 相关论文

本工具包基于以下文献：
* Xu Sun, Houfeng Wang, Wenjie Li. Fast Online Training with Frequency-Adaptive Learning Rates for Chinese Word Segmentation and New Word Detection. ACL. 253–262. 2012 

```
@inproceedings{DBLP:conf/acl/SunWL12,
author = {Xu Sun and Houfeng Wang and Wenjie Li},
title = {Fast Online Training with Frequency-Adaptive Learning Rates for Chinese Word Segmentation and New Word Detection},
booktitle = {The 50th Annual Meeting of the Association for Computational Linguistics, Proceedings of the Conference, July 8-14, 2012, Jeju Island, Korea- Volume 1: Long Papers},
pages = {253--262},
year = {2012}}
```


* Jingjing Xu, Xu Sun. Dependency-based Gated Recursive Neural Network for Chinese Word Segmentation. ACL 2016: 567-572
```
@inproceedings{DBLP:conf/acl/XuS16,
author = {Jingjing Xu and Xu Sun},
title = {Dependency-based Gated Recursive Neural Network for Chinese Word Segmentation},
booktitle = {Proceedings of the 54th Annual Meeting of the Association for Computational Linguistics, {ACL} 2016, August 7-12, 2016, Berlin, Germany, Volume 2: Short Papers},
year = {2016}}
```


## 常见问题及解答


1. [为什么要发布pkuseg？](https://github.com/lancopku/pkuseg-python/wiki#1-为什么要发布pkuseg)
2. [pkuseg使用了哪些技术？](https://github.com/lancopku/pkuseg-python/wiki#2-pkuseg使用了哪些技术)
3. [无法使用多进程分词和训练功能，提示RuntimeError和BrokenPipeError。](https://github.com/lancopku/pkuseg-python/wiki#3-无法使用多进程分词和训练功能提示runtimeerror和brokenpipeerror)
4. [是如何跟其它工具包在细领域数据上进行比较的？](https://github.com/lancopku/pkuseg-python/wiki#4-是如何跟其它工具包在细领域数据上进行比较的)
5. [在黑盒测试集上进行比较的话，效果如何？](https://github.com/lancopku/pkuseg-python/wiki#5-在黑盒测试集上进行比较的话效果如何)
6. [如果我不了解待分词语料的所属领域呢？](https://github.com/lancopku/pkuseg-python/wiki#6-如果我不了解待分词语料的所属领域呢)
7. [如何看待在一些特定样例上的分词结果？](https://github.com/lancopku/pkuseg-python/wiki#7-如何看待在一些特定样例上的分词结果)
8. [关于运行速度问题？](https://github.com/lancopku/pkuseg-python/wiki#8-关于运行速度问题)
9. [关于多进程速度问题？](https://github.com/lancopku/pkuseg-python/wiki#9-关于多进程速度问题)
10. [如何看待网络上的文稿？](https://github.com/lancopku/pkuseg-python/wiki#10-如何看待网络上的文稿)



## 作者

Ruixuan Luo （罗睿轩）,  Jingjing Xu（许晶晶）,  Xu Sun （孙栩）
