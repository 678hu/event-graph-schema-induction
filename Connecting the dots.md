We thus propose a new schema representation, **Event Graph Schema**, where ***two* event types** are connected by such **paths** containing entity-entity relations. Each **node** represents an entity type or event type, and each **edge** represents an entity-entity relation type or the argument role of an entity played in an event.两种事件类型通过包含实体-实体关系的路径连接起来。每个节点表示一个实体类型或事件类型，每条边表示一个实体-实体关系类型或在事件中扮演的实体的参数角色。

#### 动机：

**事件模式（event schema）**是蕴含在事件背后的某些原理性或规律性的模式。通过研究事件的模式能够更好地弄清楚事件演变背后的底层逻辑以及真实原因。同时，通过图结构来组织事件以及事件的论元角色，能够提高可解释性也能利用图神经网络的方法。因此，事件图模式归纳（event graph schema induction）是十分必要的。

本文假设当两个事件的实体参数是共引用或者语义相关的时，他们是相连的。

#### 任务（解决的问题）： 

这篇论文通过实例事件图（instance event graph）归纳出事件图模式，即**事件图模式归纳（event graph schema induction）**

#### Contribution：

1)一种用于新的事件模式表示的语义模式归纳框架，事件图模式，它编码丰富的事件结构和事件-事件连接，以及两个新的评估度量，以评估图模式的覆盖率和一致性。

2)一种路径语言模型，用于选择显著和一致的事件-事件路径，并构造一个具有概率性和语义一致的事件图模式存储库。

3)展示如何应用事件模式来增强端到端IE的第一个工作。

#### 方法：![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mJTsMiczUWX1ps73kAjPQxKDAYrkgst8XkFOlMGqTshjNDok0XulTDKVJW3ic6NicYu7T3YibNicAeey9w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**路径语言模型**（path language model）,通过抽取（OneIE工具）重要(salient)以及连贯(coherent)的路径来构造schema。路径语言模型由两个部分构成：自回归语言模型、邻居分类模型。

**salience** ：A good path should appear frequently between two event types

**Coherence**: Multiple paths be tween the same pair of event types should tell a coherent story, namely they should co-occur frequently in the same discourse。 

在两个任务上训练：1）学习一个自回归语言模型：给定路径中之前的边和节点预测一条边或节点

 2）邻居路径分类任务：预测两条路径共现的可能性

**自回归语言模型用来处理**单条路径的出现频率以及语义连贯性（自回归语言模型具有强大的schema构造能力）；

**邻居分类模型**用来处理多条路径是否同时出现在同一个schema中。

事件图模式的生成则是对两个事件类型之间的所有路径进行排序，选取top k的路径进行融合从而生成新的事件图模式。核心有两个，一个是从文本转换为实例图。另一个是从合并事件模式路径，形成一个大的事件模式图。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mJTsMiczUWX1ps73kAjPQxKDaArGldKtbWpjISuTrJrxPGCWmfy9NHWQtvCReyqHSxGuzdOibicIN0yw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 实验

##### 评价标准：

instance coverage and instance coherence

##### 数据集：

文中选取的数据集是ACE2005。验证集和测试集的划分如下表所示，在这个数据集上对四个baseline（frequency、unigram、bigram、trigram）进行了实验，并将结果和路径语言模型进行比较。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mJTsMiczUWX1ps73kAjPQxKD4Z5GwichibyeFuzcb85ItkeTXQ4ibTJIqPqnb03icZlumjNuZfPibV68taQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

##### 实验结果：

表格中的黑粗体表示路径语言模型的性能。不难看出，路径语言模型在l=7、5、3，三种条件下的性能（准确度、F值）都是最好的。具体说来：①PathLM性能在schema@10、schema@20下的性能都是最好的。②Trigram算法可以看作是l=3时，PathLM的一个特例，所以二者在性能上比较接近。③消融实验显示，自回归语言模型具有强大的schema构造能力，同时邻居分类模型也能够提高模型的性能。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mJTsMiczUWX1ps73kAjPQxKDQAAa4bqOyloV4hCK57phrStTYicHXqqOQD7oU4oUx6VeEIglsuIZ0Dg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### **Remaining Challenges**:

A major challenge in schema induction is to auto-matically decide the type granularity. 

#### **Related Work**

**Atomic Event Schema Induction.**:单个原子事件的事件类型和参数角色。

**Narrative Event Schema Induction:**

**Path-based Language Model.** 

**Graph Pattern Mining.**

#### Future work:

In the future, we aim to extend graph schemas to encode hierarchical and temporal relations, as well as rich ontologies in open domain.

