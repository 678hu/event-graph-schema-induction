Future is not one-dimensional: Graph modeling based **complex** event schema induction for event prediction (*EMNLP*, *2021*)

解决的问题：复杂事件理解（包含了多个原子事件（及其参数）、关系和时间顺序）

复杂事件模式可以用来定义特定类型的复杂事件的典型结构

模式图可以看作是实例图的摘要抽象，捕获重复出现的结构。

#### Motivation:

事件图模式来辅助事件预测，进而提高事件预测的性能。通过**图神经网络**进行事件图模式推理（event graph schema induction），并将推理出的schema运用到事件预测当中。

以前关于事件模式归纳的工作要么关注原子事件，要么关注线性时间事件序列，忽略了通过参数和参数关系的事件之间的相互作用。

对于每种复杂事件类型，推理出一个模式库（ that is probabilistic, temporally organized and semantically coherent）

#### Contribution:

1)跨文档级复杂事件上的概率时序图模式推断：通过共引或相关的参数来捕捉独立事件之间的时间动态和联系。

2）应用图生成方法来归纳事件模式。

3）使用复杂事件模式进行事件类型预测的工作，并产生多个具有概率的假设。

4）提出内在和外在的评价指标

5）发布了一个包含6399个文档的新数据集，其中手动注释了gold-standard 模式

#### Method:

graph-based schema representation: events, arguments, temporal connections and argument relations

文中提出了一种基于图神经网络的生成式模型(*Temporal Event Graph Model* )。通过现有的部分图谱，不断的生成新的结点和边。同时结合共指消解以及事件时序顺序预测，得到最终的schema。然后再进行schema-guided prediction

1）**构建实例图**（每个实例图都是关于一个复杂事件）

OneIE : 提取实体、关系和事件 --> perform cross document entity and event coreference resolution over the document cluster of each complex event-->进一步进行了事件-事件时间关系的提取来确定事件对的顺序-->在提取之后，为每个复杂事件构造一个实例图，其中共引用事件或实体被合并。我们将孤立的事件视为模式归纳中的不相关节点，因此在图构建过程中，它们被排除在实例图之外。

2)**Temporal Event Graph Model** 

最大化每个实例图的概率,，基于前面的图G<i，我们预测一个事件节点ei及其参数来生成下一个图Gi.(首先根据概率生成事件节点，然后基于IE ontology添加参数节点，之后预测新生成节点与现有节点的关系，最后预测新事件与现有事件的时间关系)

在传统的图生成设置中，**节点生成的顺序**可以是任意的。然而，在我们的实例图中，事件节点是通过时间关系来连接的。我们将事件排序为一个有向无环图（DAG）。考虑到每个事件可能同时有“之前”和“之后”的多个事件，我们通过使用广度优先搜索遍历图来获得生成顺序。

**事件生成：**为得到当前图的表示，在所有事件上应用一个graph pooling。初始化潜在节点和边的值为0，通过消息传递在每个生成步骤中进行更新。本文采用了均值池运算。之后，将通过一个全连接层来预测事件类型，一旦我们知道了ei的事件类型，我们就会将它在IE本体中定义的A（ei）中的所有参数添加为新的实体节点。

3）**Edge-Aware Graph Neural Network **边缘感知图神经网络

使用CNN更新node embedding，在在图上运行GNN之前，我们首先在新生成的事件和所有以前的事件之间以及新实体和以前的实体之间添加虚拟边。虚拟边使新节点的表示能够聚合来自以前节点的消息

为了捕获边缘类型的丰富语义，我们在图传播过程中传递具有边缘感知的信息：学习每个关系类型r和参数角色a的向量表示。

考虑到时间边的方向是重要的，我们通过给传出和传入的顶点分配两个单独的权重矩阵来参数化这条边上的消息：

然后使用来自其本地邻居N（ei）的消息来更新事件节点表示ei，类似于实体节点表示。

4） **Co-referential Argument Generation** 共引参数生成

在更新节点表示之后，我们将检测每个参数的实体类型，并预测该参数是否与现有实体进行了共引用。

5） **Entity Relational Edge Generation** 实体关系边生成

确定要保留的虚拟边，并为它们分配关系类型，我们将关系边缘生成概率建模为关系类型上的分类分布。

#### Experiment:

##### 评价标准：

*schema matching* and *instance graph perplexity* （intrinsic）

*schema-guided future event prediction* (Extrinsic)

##### 数据集：

本文采用了两个针对不同场景的数据集（general IED、IED），分别对一般场景、特定场景下模型的性能进行了测试。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEAggrW73ZBicoNNpogT0JAb74ucYeQe0T74PE4Ficia4S8nuec0vo73De0w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

##### 实验结果：

事件模式归纳的实验结果如下图3所示，采用Event Language Model和Sequential Pattern Mining作为baseline，同时进行了消融实验，测试了本文所提出的Event Graph Model在不进行实体共指融合时性能有何变化。从实验结果中不难看出①Event Graph Model对于关键事件、长序列的记忆能力更强，各项指标都有一定的增益；②在一般场景和特定场景下，Event Graph Model均表现很好且性能稳定，说明本文所提出的模型有更好的泛化能力，适用于各种场景；③通过消融实验对比可以发现，在进行了实体共指融合时所生成事件模式的事件复杂度（Event Perplexity）比不进行实体共指融合时有大幅降低，进一步验证了实体共指消融这一步骤的必要性。![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEARJ3Tn6l7BO8ash3cXMn1L3EGPpcYMXZxFeo3Ed50n890Ogwhb7QYCw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

 事件预测的实验结果如下图4所示，采用Event Language Model等三个模型作为baseline，同样测试了本文提出的Event Graph Model，并进行了消融实验。通过实验结果可以看出①针对预测任务，Event Graph Model有很大的性能提升；②通过消融实验验证了实体共指消融对事件预测任务的必要性。![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/7FDdzianu7mKUEtvXSXVK3OibBt2YHGwEAE0OCpVEue51NPunpyQWBCtwleXvVpicJkUSSM2q9twe2Yia3H4Sx78FQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

#### Related Work:

