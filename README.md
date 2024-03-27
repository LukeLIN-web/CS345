# CS345

ppt在 https://sands.kaust.edu.sa/classes/CS345/S24/schedule.html

课前 24小时, 周六早上10点, 周二早上10点前要提交review.

要学习英语写作, 不能用chatgpt. 

### 怎么give talk

要让people足够重视你的paper.

#### connect with audience

估计听众的Background. 一般来说听众不可能比你expert.  所以一些介绍是必要的. 大概要三分之一的slide 来介绍,然后才能进入你的solution.

就算人非常多, 也尽量用眼睛看观众而不是盯着讲稿.

#### 内容

objective

problem statement

overview

solution main idea

evidence that your solution is good

recap/summary of the main points

一开始, 用一些hook吸引观众, 比如最近的大新闻.

 如果可以的话, make a story. build anticipation, 有惊喜. 

开头一句话,总结一下你工作的topic是什么, 是数据中心, or 是serverless , 是GNN系统.

结尾, 放一些联系方式, website, 不要在ppt上只写一个大大的thank you.

#### 语音

用声音的大小, 停顿 来突出你的重点.

#### visual

字越少越好. 用大字体. 尽量用图和动画.  不过你要解释图, 比如我的red line比之前工作的blue line 低. 

如果有别人的work, 要引用他们. 引用自己的也可以. 

回答问题

你可以提前猜一些问题. 准备好答案. 

如果没听懂问题, 你可以重复/summary一遍. 

如果不会回答, 可以说不知道, 或者回避问题, 说我们可以talk offline, 或者你可以问我导师他更懂. 

第一个paper talk可能要准备两周. job talk要准备一个月. 

参考资料 :  How to avoid death By PowerPoint | David JP Phillips | TEDxStockholmSalon https://youtu.be/Iwpi1Lm6dFo?si=R_aGJ2_7okJxnn0-

### 云计算

Iaas  : VM , disks

PaaS:  web, mapreduce, sagemaker

SaaS:  email, github, chatgpt.

Faas:  webapps, IOT. 

#### trend

1. SSD 和NVM取代 HDD
2. ethernet  growing
3. bandwidth  to storage, 也就是从ram和ssd读取数据, 是bottleneck.  

这些带宽数据不是背诵的,而是应该在实际的内存读取测时间中用到去查去理解的. 

### 怎么高效科研

保护你的uptime, 高效时间. 不meeting, 不talk, 关闭email, silence phone, leave shared office, 给自己列出todolist在uptime做, 不要在uptime无所事事. 

总是记下你的idea, 有机会多记下细节, 以后需要的时候可以节省时间. 因为你以后会学会更多方法. 



### 怎么画图

首先要知道你需要用图支持什么论点.

需要达到高**Data-Ink-Ratio**,  比如增加标签说明, 

paper和slide用的图也不一样, slide时间更有限, 所以细节要更少,  paper的图不能太小, 否则打印出来就没法看. 

有机会的话图中加入error bars来表示uncertainty. 要在captions解释你用的是标准差还是方差.

chatgpt 来写matplotlib的代码,画图咔咔快, 输入输出prompt一下, 几秒就生成好了.

decouple画图和数据处理, 画图的时候不要做任何费时的事情.  raw data-> plot data-> figure.

必备的几个元素: 解释 x y axis,  unit.

CDF图怎么画:  •https://colab.research.google.com/drive/1OEG16-J2snSk5RQFe07HJ4nE4kKhaZM3?usp=sharing

 画图, 可以在baseline 画一条虚线. 

#### 科研

你在哪里有啥资源, 就应该利用好你独特的资源来发论文来工作.

不应该相信任何作者任何big name的conclusion, 总是要质疑. 
