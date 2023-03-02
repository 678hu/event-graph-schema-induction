Event Schema Induction with Double Graph Autoencoder (*NAACL*, *2022*)

**基于双图自编码器的事件图模式归纳**

获取复杂事件的全局结构信息

#### 动机：

事件图模式蕴含着事件背后的演化逻辑，归纳出事件知识图谱的模式有利于我们抓住事物的本质，并对下游的任务有很好的启发作用

为了从历史事件中归纳事件模式，以前的工作使用逐事件的方案，忽略整个模式图的**全局结构**。

#### 方法：

为了归纳出EKG schema，文中首先对已有的instance graph进行抽取，得到event skeleton。接着将skeleton作为第一个自回归编码器的输入（学习全局结构），输出就是重构的schema skeleton。再对得到的schema skeleton进行论元补全，将补全之后的skeleton输入到GCN当中，重建实体-实体关系，以及检测共参照实体。最后就可以得到完整的schema。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEAvb7F0JtC1Zk31zoVLPZIlQBIsCOQhZdPDxMUDy2uDiaWAHbhBlxFvFw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

具体来说，我们的模型包含两个图的自动编码器，它们以**分层**的方式组织： (1)为了对事件图的骨架进行建模，我们为有向无环图（DAGs）设计了一个高级变分图自动编码器。事件骨架是事件图的一个子图，它由显著的事件及其时间顺序组成，表示事件演化的基本结构。(2)**以事件骨架为全局上下文**，我们通过引入一个基于图卷积网络（GCN）的低级图自动编码器来装饰骨架中的实体节点。它将一个扩展的事件骨架作为输入，并通过添加**共引用实体和实体-实体关系**来重建原始事件图。将事件模式归纳过程分解为两步，从而避免了直接重构整个图的需要，并在高级层次（事件模式骨架）和较低层次（实体-实体关系）上有效地提高了模式学习。

#### 实验：

两个baseline：1）*Temporal Event Graph Model* (TEGM) 2）*Frequency-Based Sampling* (FBS).

评估标准：1）*Event type match*事件类型匹配 2）*Event sequence match*.事件序列匹配。

3）*Node/edge type distribution*.节点/边缘类型分布。4）*Maximum common subgraph*最大公共子图

##### 数据集：

General-IED，Car-IED，Suicide-IED

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEA7H74JTVGMA1PDQ92U98zJRd0gBGIUF2A46hEl7SW2Q17ibkcFCqgBuA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

##### 实验结果：

实验结果如图（图3）所示，所使用的baseline为时间事件图模型（TEGM）和频率事件图模型（FBS），本文所提出的模型为DoubleGAE。其中TEGM模型就是论文[2]中提出的模型。通过对比可以发现：①整体来看，DoubleGAE与baseline相比取得了显著增益，体现了全局依赖性对于事件模式归纳的重要性；②DoubleGAE在Event type和Event seq match指标展现了很大的增益，说明DoubleGAE有能更好的关注到关键事件且有良好的长序列记忆能力；③由于FBS是根据实例图中某条路径的出现频率构造schema，所以在KL散度指标展现出一定优势，也是一个很有竞争力的模型。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEA44vrDqozETRZRibcSEwuFicPia6V8kHherQUgXWmlZ0thmlhNTZKKuHfQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

另外，通过TEGM与DoubleGAE生成的事件模式对比可以发现，通过TEGM生成的事件模式（图4（a））中Attack连续多次出现，而实际上在一次攻击之后往往不会立即重复多次攻击，显然，这样的事件模式并不合理；不过在DoubleGAE模型生成的事件模式（图4（b））中不包含这样的冗余结构，在局部和全局上都有更加清晰、正确的逻辑结构。基于此也验证了全局结构信息对事件模式归纳的重要性。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEA7gdSicbABtHa5mXtoKyAr6axXuVkxsksiaeO0o0JtmVPNexNMmR3edyQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)