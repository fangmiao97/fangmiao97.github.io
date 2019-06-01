---
title: 基于Spark Streaming的网站日志实时流处理
date: 2019/06/01
categories: 
    - 毕业设计
---

## 摘要
随着大数据时代的到来，数据总量不断增加，同时分布式存储与计算技术也在快速发展，如何从海量、分散的数据中提取出有价值的信息，已经成为各个行业的迫切需求，对数据的实时处理和响应变得越来越重要。

本文设计了基于Spark Streaming的网站日志实时流处理系统，针对通用音乐网站两种类型的日志：网页浏览日志和网页行为日志，进行了实时采集、传输、处理、分析、持久化和可视化。本文完成了以下几个功能：1）使用Flume采集日志数据，解决了分布式日志采集和聚合的问题，同时对日志进行了分流；2）使用消息队列Kafka解决大量日志数据传输问题，并为下游数据处理分析Spark Streaming提供实时日志数据源；3）使用Spark Streaming进行多种类型日志数据的实时处理分析，达到秒级处理和阶段数据处理的能力，并将分析结果数据持久化至HBase中；4）使用前后端分离的开发模式完成分析结果的可视化展示。系统实现了将大量分散的日志数据进行实时整合，并完成分析计算，最后从多种维度进行信息展示。

关键词：实时流处理；分布式计算；Spark Streaming；Flume；前后端分离

## ABSTRACT

With the advent of the era of big data, the total amount of data continues to increase, and distributed storage and computing technologies are also developing rapidly. How to extract valuable information from massive and decentralized data has become an urgent need of various industries. Real-time processing and response of data is becoming increasingly important.

This paper designs a real-time stream processing of website logs system based on Spark Streaming. It performs real-time collection, transmission, processing, analysis, persistence and visualization for two types of logs: universal web browsing logs and web page behavior logs. This paper has completed the following functions: 1) Using Flume to collect log data, solving the problem of distributed log collection and aggregation, and diverting the log at the same time; 2) Using message queue Kafka to solve a large number of log data transmission problems, which providing real-time log data source to the downstream application to process and analysis data; 3) using Spark Streaming to perform real-time processing and analysis of various types of log data, achieving the capability of second-level processing and phase data processing, and persisting the analysis result data to HBase; 4)Visualization of the results of the analysis using a development model with front and rear separation. The system realizes the real-time integration of a large number of scattered log data, completes the analysis and calculation, and finally displays information from various dimensions.

KEYWORDS: Real-time stream processing; Distributed Computing; Spark Streaming; Flume; Front and rear separation

## 1绪论

### 1.1研究背景与意义

随着DT（Data Technology）时代的到来，数据总量不断。预计到2020年，全球数据总量将超过44ZB，同时根据图灵奖获得者杰姆·格雷提出的“新摩尔定律”，每18个月，全球新增数据总量是自计算机诞生以来所有数据量之和。数据作为一种新的能源，被各行各业广为利用，为企业生产经营提供有价值的信息。同时，“爆炸式”增长的数据也对现有的数据采集、传输、分析和存储技术提出了挑战。

互联网行业可以从多种多样的数据源采集信息，例如，服务器日志、网关日志、数据库日志和业务信息等。这些数据通过企业商业智能平台的挖掘分析，为企业内部以及各样相关者提供有价值的信息，例如，电商行业对于用户画像进行分析，进行精准营销和“千人千面”的召回数据，提高转化率；金融行业对于风险监控的要求较高，通过大数据分析，将提高企业的风控效率和准确率。而浏览器的页面型产品/服务的日志在众多数据源十分突出，一是其自然地提供了页面浏览（展现）的详细情况，以及具有定制化采集页面交互日志的能力，所以企业能够在这些日志数据中提取到大量可用有用的信息；二是部分日志信息具有很高的时效性，这是指采集到的日志数据需要实时分析、处理，并召回实时响应数据，为用户提供更好的体验。数据的离线和实时分析都是互联网行业“闭环”的重要部分。

各大公司以及开源社区都提出了各种可用的计算模型和解决方案，为数据采集、传输、存储和分析提供了多种有用的工具。而目前最为火爆的工具之一就是Spark，它在Spark Core的基础上提供了多种高级实现，其中Spark Streaming使数据的实时处理成为可能。

本文以一个通用音乐网站为背景，基于Spark Streaming对网站浏览日志和交互行为日志进行实时流处理，完成了从数据采集、传输、清洗、处理、持久化以及可视化的全流程。系统实现了网站服务器浏览日志的采集，以及通过前端埋点的方式采集可定制化的数据信息，使用Flume和Kafka向下游分流和传输数据。使用Spark Streaming清洗和处理数据，并将相关业务指标数据持久化到HBase中。可视化使用Springboot和React进行前后端分离的实现。

本系统提供的数据有：
* 页面浏览数据，包括Page View和网站各类目访问量，访问趋势等多维度的指标可视化数据，帮助相关人员进行网站的优化以及搜索引擎优化。
* 页面交互信息数据，包括音乐网站情景下多种用户操作行为的记录与分析，并实时展示多维度的分析结果。

### 1.2研究现状

日志分析是在海量离散的日志数据中提取出有价值的，符合人们认知的信息。网站日志分析则是在页面浏览、页面行为等日志中得到多种维度的数据，供网站不同职能人员使用。例如，运营人员根据网站浏览量、各类目的访问情况等制定相应的运营方案；高层团队领导根据网站各业务转化率等指标灵活制定网站产品发展战略；开发人员根据网站服务性能数据优化产品性能，保障产品质量。如今已有许多商业上可行的方案，比如百度统计、神策数据等，但是它们要么关注仅仅关注访问量的统计，要么关注用户页面行为的统计。

日志分析在国内外发展都十分迅速。最开始由于网站用户访问量不大，业务量规模也相对较小，整个网站的各个功能模块的日志都由专员进行分析。一般情况是远程登录各个节点来分析日志。随着业务量的不断增大，服务器以及功能模块的不断增多，日志分散在大量机器实体上，这给日志数据的采集、传输、分析和存储带来了挑战。

Google用三篇论文提出了奠定大数据基础的GFS、Big Table和MapReduce，它们分别为分布式文件系统、海量数据存储和分布式计算提供了可行的方案。Apache的顶级项目Hadoop将它们进行了开源实现。因此，技术人员能够运用Hadoop对海量的日志数据进行分析，处理高吞吐、批量处理的业务场景。但是随着业务场景的复杂化，如互联网金融中的实时风控、电商中的个性化推荐数据召回等的出现，运用Hadoop MapReduce完成的离线数据分析已经不能满足企业发展的需求。Spark于2009年诞生于UC Berkeley的AMP实验室，并与2013年贡献至Apache社区，成为近年来最火热的大数据开源项目。

Spark是基于内存的并行计算框架，也是基于MapReduce计算模型的分布式计算框架，其解决了Hadoop MapReduce的诸多问题，例如不能重用在迭代式机器学习和图计算中经常需要的中间结果，Hadoop MapReduce需要将这些中间数据进行复制、硬盘I/O和序列化。而Spark实现了弹性分布式数据集（RDD），一种基于内存的集群计算容错性抽象，能够很好地适应各种业务场景。Spark Streaming是在Spark Core之上的高级API。Spark Streaming提供了表示连续数据流的、高度抽象的被称为离散流的Dstream，可以通过多种数据源创建Dstream，为下游实时流处理使用。所以Spark Streaming为当前实时流处理的场景需求提供了可靠的上下文。

此外，使用Flume与Kafka为海量日志的采集和传输提供了解决方案。Flume是一个高可用、高可靠的分布式海量日志收集、聚合和传输系统，通过Flume可以将分散在各个服务器上的日志收集起来，以供下游日志数据处理分析的使用。Kafka是一种高吞吐量的分布式发布订阅消息系统，在大型网络中运用其对网站的服务性能进行扩展，能够保证所有服务请求不丢失，同时Kafka作为大型网站的基础设施，也为实时日志流处理提供消息队列的服务。

目前对于实时流处理有许多解决方案，日志采集、传输、分析和存储的技术也有许多种选择。不同的解决方案/工具是由特定场景下提出的，寻找最合适的工具完成系统设计十分关键。

### 1.3论文结构

本文将全文内容划分为五个章节进行展开，其组织结构如下：

第一章：绪论部分。简要介绍了网站日志实时流处理的研究背景和意义，并对现有大数据分析技术进行了简单的介绍，同时描述了本文设计的主要工作。

第二章：系统相关理论和技术介绍。简要介绍了数据处理和日志分析的出发点和要点，针对日志采集、传输、处理、分析的难点进行介绍，并提供了现有可靠的解决方案或工具。

第三章：系统需求分析与架构设计。基于本文设计的系统出了分模块的需求，并整合最佳的解决方案，设计了系统整体架构。

第四章：从日志数据产生与采集、日志数据传输、处理、分析与持久化以及分析结果可视化三个方面进行了详细设计与实现。

第五章：对本文设计的系统进行总结，并提出接下来要进一步完善的需求。

## 2相关理论与技术

### 2.1互联网中的闭环理论

#### 2.1.1闭环理论

闭环理论是源于系统论和控制论的一种管理思想，其核心是通过有效的管理和控制手段，构成一个连续封闭的管理回路，使得系统中的每一个环节都能有效的衔接。最终达到提升管理水平，实现既定目标的目的。闭环系统的典型特征有：可控性强、首尾相连、循环往复。闭环式的组织能够高效地利用资源，灵活应对外界变化，达成组织目标，并在这个过程中不断自我提升。

闭环理论强调“可控性”，在组织的运行流程中，尽可能的收集信息，并与原计划进行匹配，出现偏差则进行调整和修正。从而实现系统的高效演进与自我提升。

#### 2.1.2互联网闭环

闭环理论最初由休哈特于1930年提出，现在已经衍生运用于各行各业。对于计算机互联网这样的强调系统和控制的行业，闭环理论运用十分广泛。在互联网行业中运用闭环理论的系统通常包括：验证、反馈、分析和控制这几个部分。直观的运用实例如下：

* 产品开发、运营优化。如产品新特性发布前的灰度测试、电商及内容类产品的实时推荐系统等。
* 互联网服务。如O2O线上到线下等各类分销平台、金融风控系统等。

为了达到产品的不断提升，并对外提供更好地服务，互联网行业必须将整个系统的各部分形成完整闭环，而数据挖掘与分析是补完闭环的关键环节。网站日志实时分析作为网站系统闭环的一部分，通过分析来自用户的真实访问情况和行为情况反馈，为运营和决策人员提供有价值的信息，用以不断完善产品质量。此外通过收集数据为产品的其他功能，如推荐投放系统提供一手数据资源，完善相关功能，使得反馈结果实时触达用户。整个系统各个部分都为系统的提升做出贡献，并使之成为一个可提升的完整闭环。

### 2.2网站日志采集

浏览器的页面型产品/服务的日志采集可以分为如下两大类。

#### 2.2.1页面浏览（展现）日志采集

页面浏览日志是指当一个页面被浏览器加载呈现时多采集的日志。此类日志是最基础的互联网日志，也是目前所有互联网产品的两大基础指标：页面浏览量（Page View，PV）和访客数（Unique Visitors，UV）的统计基础。页面浏览日志是目前成熟度最高和完备度最高，同时也是最具挑战性的日志采集任务。

页面浏览日志通常是记录在应用服务器上，如Nginx的access.log。

#### 2.2.2页面交互日志采集

当页面加载和渲染完成之后，用户可以在页面上执行各种操作，随着前端技术的发展用户可以与页面上的元素完成很多互动操作，如启动、点击、拖拽和曝光等，这些行为信息价值巨大，设计者通常会要求采集用户的互动行为日志，以便通过量化获知用户的兴趣点或者优化体验。而行为数据属于低价值密度数据，其数量十分巨大，需要强大的数据处理能力和分析能力才能有效利用。

行为日志的记录有多种方式，网页应用常用前端埋点的方式进行采集。

### 2.3 ETL过程与Flume

#### 2.3.1 ETL

ETL（Extract-Transform-Load）是一种数据仓库技术，用来描述将数据从数据源抽取（extract）、转换（transform）和加载（load）的过程。这一过程常用于数据仓库技术，但是在目前大数据应用中也十分重要。具体到日志分析类应用，由于服务器众多，同一类型的日志分散在不同的机器实体上。同时不同类型的日志有不同的结构，为了便于分析通常情况下需要同一结构。此外对于各种行为日志需要又不同的下游数据分析应用来处理，这就涉及到一定程度上的分流。因此在网站日志实时流处理中需要有一个ETL过程来进行分散数据的采集和一定程度的过滤清洗，使得下游应用能够高效运作。

目前，Apache Flume十分适合这类ETL过程的业务场景。

#### 2.3.2 Flume

Flume是一个高可用、高可靠的分布式海量日志收集、聚合和传输系统，其能够将不同源的日志数据汇聚到统一的数据存储中；Flume具有基于流式数据的简单且灵活的架构，同时具有优秀的容错机制和故障恢复机制；Flume运用简洁又灵活的数据模型，使得其十分适合在线分析应用。

Flume不仅可用于日志数据的聚合。由于数据源是可定制的，Flume还可以传输海量的事件数据，包括但不限于，网络流数据、社交媒体产生的数据、电子邮件数据和各种可能形式的数据。

Flume处理数据的最小单位是一个Flume Event。Flume Agent是一个常驻JVM的进程，用于处理从外部源传输来的event。Agent由source、channel和sink组成，如图2.1所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366186537/0)

用户可以选择多种数据源作为source，如avro source、thrift source和exec source等。在本文设计中，对于页面浏览日志使用exec source，执行系统读取指令采集log文件中新增的日志信息。

对于页面行为日志，采用avro source监听特定的网络端口采集行为信息数据。

对于采集信息的转换，Flume为source中提供了interceptor（过滤器）这个功能。在本文设计中，将运用过滤器对页面行为信息根据行为类型进行分流，以便下游不同数据处理应用使用。

Flume的各个单位事件数据通过sink传递给下游应用使用。本文设计中使用kafka sink传递给下游kafka生产者使用。此外，为了防止事件数据过多，使用channel起到缓冲的作用，可以通过设置channel缓冲的大小来确定其承载力。

### 2.4 Kafka

消息队列中间件是分布式系统中重要的组件，主要解决应用解耦，异步消息，流量削锋、日志处理等问题，实现高性能，高可用，可伸缩和最终一致性架构。

Kafka是经常用于日志处理的消息中间件，解决大量日志传输的问题。上游日志采集模块将采集到的数据写入到Kafka队列中，Kafka消息队列负责日志数据的接受、存储和转发，下游日志分析处理应用订阅kafka队列中的日志数据。

本文设计中下游日志分析应用即是Spark Streaming作业。由于日志的数量很多，不同类型日志的处理分析逻辑不同，所以引入kafka消息队列。一方面防止日志数据过多淹没下游应用，另一方面按消息的topic进行分流消费。

### 2.5 Hadoop与Spark

Google用三篇论文提出了奠定大数据基础的GFS、Big Table和MapReduce，它们分别为分布式文件系统、海量数据存储和分布式计算提供了可行的方案。Apache的顶级项目Hadoop将它们进行了开源实现。因此，技术人员能够运用Hadoop对海量的日志数据进行分析，处理高吞吐、批量处理的业务场景。

Hadoop是一个分布式系统基础框架，用户可以在不了解分布式底层细节的情况下，开发分布式程序，充分利用集群的威力进行告诉运算和存储。Hadoop诞生于2005年，在社区和众多技术人员的支持下，Hadoop生态圈逐步扩大，形成了一整套用于分布式数据处理分析存储等功能组件。其中最重要的组件有，分布式文件系统HDFS、分布式计算框架MapReduce、分布式列存储数据库HBase、分布式协作服务ZooKeeper、日志收集工具Flume、分布式资源管理器Yarn等。这些组件提供了各种业务场景下数据处理需求的解决方案。其中MapReduce是一种分布式计算模型，用以进行大数据量的计算。其屏蔽了分布式计算框架的细节，将计算抽象成map和reduce两个过程，具有高可靠性、高扩展性、成本低等特点。因此MapReduce被广泛运用与大规模数据分析。

但是随着更多复杂数据分析处理场景的出现，如迭代式计算和交互式数据挖掘等，MapReduce计算模型的出现了缺陷。首先，MapReduce将数据处理的过程抽象成2个阶段，map和reduce，虽然高度抽象，技术人员只需要编写两个函数即可。但是高度抽象带来的问题是表达能力有限，当出现更复杂的计算要求时，不得不将map和reduce两个过程复杂化或者增加更多的MapReduce任务。同时，MapReduce过程中的计算结果都需要进行磁盘I/O，这样当需要多个MapReduce任务写作时，导致任务之间的衔接涉及I/O开销，不能直接重用中间结果。此外，在前一个任务执行完成之前，其他任务无法开始，难以胜任复杂、多阶段的计算任务。

MapReduce缺少对分布式内存的应用，而Spark在借鉴了Hadoop MapReduce有点的同时，很好地解决了MapReduce所面临的问题。首先Spark实现了一个分布式内存抽象的概念--弹性分布式数据集（以下称为RDD），其能够让开发人员以容错的方式在大规模集群上进行基于内存的计算。RDD是分布式内存的一个抽象概念，提供了高度受限的共享内存模型，这使得Spark计算任务能够在内存中重用中间计算结果。Spark的计算模式也属于MapReduce，但是不限于map和reduce操作，将数据操作抽象成对RDD的一系列粗粒度转换，如map、filter、reduce、group等，比MapReduce更加灵活。由前两个特性，Spark实现的RDD能够拥有很好的容错性能，Spark计算模型将对RDD的操作记录成lineage，即血缘关系，在真实计算任务时并不立即对数据进行转换操作。这样当出现中间计算结果缺失时，只需要根据lineage重新计算数据即可。此外，Spark是基于RDD的lineage形成的有向无环图DAG来进行任务调度的，要优于MapReduce的迭代执行机制，如图2.2所示，实线框为RDD，不同RDD之间的转换形成DAG，Spark通过这个DAG进行任务调度。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366311701/0)

针对流式数据处理场景，Spark在Spark Core的基础上实现了更高级的API--Spark Streaming。Spark Streaming是提供了表示连续数据流的、高度抽象的离散流（DStream）。DStream本质上是对RDD的一层封装，其中的每一个RDD都包含来自一个时间间隔的数据，如图2.3所示。DStream是一个没有边界的集合，没有大小限制，其代表了一个时空的概念。对DStream的操作，具体到每个时间段，就是空间的操作，也就是对时间间隔的对应批次数据的处理。Spark Streaming对于DStream的操作实质上就是对每个RDD应用批量操作。Spark Streaming将对流数据的处理抽象成对DStream的处理，而这种抽象是的开发人员能够像操作RDD一样操作DStream，所以说Spark Streaming提供了实时流处理的上下文。此外Spark Streaming支持从多个数据源创建DStream，如图2.4所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366347719/0)

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366361643/0)

### 2.6前后端分离

前后端分离是指在网站开发阶段前端与后端各司其职，前端只要根据设计还原出页面的逻辑展示，后端只要处理根网页数据相关的业务逻辑，并向前端透出需要的接口即可。这种模式能够使前后端并行开发，大大提高效率，并将前后端一定程度上解耦。本文设计中网页技术部分采用前后端分离的模式，模拟用户页面行为的产生，并且进行分析数据结果的可视化。前端采用react技术栈，后端采用SpringBoot。

React是Facebook公司开源的前端框架，是近年最火热的技术，国外Facebook、Instagram和国内知乎、蚂蚁金服等都在使用。React的思想是将前端所有元素组件化，将所有需要的UI控件甚至逻辑控件抽象成组件进行开发。每个组件维护自己的状态逻辑变化。React技术不断发展，社区中也不断贡献出特定场景下优秀的解决方案。

SpringBoot是由Pivotal团队开发的框架，其能够简化新Spring应用的初始搭建以及开发过程。SpringBoot使用很多特定或默认的方式进行配置，简化了开发人员的准备工作时间，可以专注于真正业务的实现。SpringBoot简化了基于Spring的应用开发，通过少量的代码就能创建一个独立的、产品级别的Spring应用，特别适合实现目前最火热的微服务架构。本文设计选择SpringBoot作为后端服务框架。

### 2.7 本章小结

本章介绍了互联网中的闭环理论以及针对日志数据采集、传输、处理、分析、持久化以及可视化的可靠的开源工具，为本文设计的系统中的各个需求场景提供了最优的解决方案。

## 3系统需求分析与架构设计

### 3.1系统需求分析

#### 3.1.1系统概述

本文设计以一个通用音乐网站的日志为分析对象的流处理系统，完成对日志进行实时采集、传输、清洗、处理、分析、持久化和可视化，最终得到网站页面浏览和页面行为日志数据的相关数据分析指标结果等有价值的信息，展示实时流处理的能力。按照数据流处理流程可将本系统分为以下几个部分：日志数据产生与采集模块、日志数据传输模块、日志数据处理分析模块、分析结果持久化与可视化模块。数据流程如图3.1所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366469597/0)

音乐网站的日志分为两种类型，页面浏览日志和页面行为日志。对于两种类型的日志需要使用统一的日志收集工具进行收集，以便下游组件的使用；数据量庞大的日志数据需要使用消息队列进行传输，达到流量削峰的目的；从消息队列得到的日志数据进过分布式计算工具进行处理计算分析，持久化至存储设施中，最后将分析结果与网站业务相关的结构化数据进行更具体的连接操作完成分析结果的可视化展示。

#### 3.1.2数据产生与采集需求分析

两类不同的日志，页面浏览日志和页面行为日志，采用不同的方式产生。对于页面浏览日志中的每一条浏览日志需要是可配置的结构化数据，这样便于下游应用的清洗等操作,这些日志数据还需要存储在特定的位置，便于采集；对于页面行为日志，同样需要有一定的数据结构，便于在下游对不同的行为日志数据进行分流，但是页面行为日志可以有一定的定制化内容，因为不同的行为可能要采集的数据是不同的，因此行为日志的结构要保留一定的灵活性。行为日志数据的产生不能对用户体验产生影响，也就是说对于用户来说是透明的。

日志数据的采集需要考虑到网站服务分布式的特点，也就是各类型日志都是分布在不同的服务器实体上，因此需要使用一个能够将分布的日志快速收集为全量日志数据的工具，同时为了与下游数据传输组件进行配合，采集工具需要具有一定的分流功能，即是能够将不同种类的日志信息先进行汇总，再向同一目的分发。

#### 3.1.3数据传输与处理模块需求分析

海量日志数据如果不加控制地发送至下游应用进行处理分析，会造成很多问题，最严重的就是超过应用的处理限度而不能正常服务，从而丢失数据。所以必须使用消息中间件解决大量日志数据传输的问题。消息中间件还需要有高可靠性和容错的能力。

对于日志数据的处理分析模块，需要根据不同的日志类型和日志的种类进行不同的处理分析，也就是说页面浏览日志与页面行为日志分析的逻辑是不同的，对于不同行为的分析也是不同的。分析处理模块需要具有实时流处理的能力，以及一定的容错能力。同时还能分析复杂的数据场景，比如处理一定时间窗口内的所有数据。

#### 3.1.4数据持久化与可视化模块需求分析

分时结果数据的持久化需要考虑海量数据的特点，同时对于数据存储内容还要有一定的灵活性，这是指存储的分析结果内容应该能够根据业务的需求进行调整，能够动态地增加字段而不用修改表结构。

可视化模块作为系统最终对外展示的部分，体现了整个系统的风格。优雅直观的界面和简洁直接的交互，不会让使用者产生疲倦感，同时有意愿继续使用和对产品提出改进意见。分析结果数据应该从各个维度进行数据的展示，维度由大到小，逐渐细化，帮助用户更好的观察数据。可视化模块是将一条条结构化的数据反映成直观形式表示的最后一步。

可视化部分分为：今日数据概览、历史数据查询和网页行为分析。详细需求如下：

* 今日数据概览

展示网站各维度的今日实时数据，包括实时PageView、七日PageView走势、今日实时PageView走势、网站各类目访问量、各类目访问量走势、今日来源网站数据汇总和热搜词云。以供运营人员优化网站和优化搜索引擎结果等。

* 历史数据查询

可以选择所要查询的日期，查看所选日期的各项数据指标，同时增加以天为维度的30日访问量比较，可用于帮助网站各级别管理人员制定网站今后的内容发展方向，辅助决策。

* 网页行为分析

能够模拟音乐网站上的用户行为，如歌曲播放、收藏和评论等。从各个维度展现行为日志分析数据，如今日歌曲播放量排行，近一小时内热点歌曲排行、歌曲风格统计、热门歌曲标签云、热评歌曲和热门收藏歌曲等。各维度数据供网站运营人员优化网站歌曲内容。

### 3.2系统架构设计

#### 3.2.1总体架构

本文设计的网站日志实时流处理系统，采取分布式的架构，遵循低耦合的原则。总体架构分为四部分：日志数据产生、日志采集与传输、日志处理分析和持久化以及分析结果可视化。总体架构如图3.2所示，页面浏览日志与页面行为日志采用不同的方案产生；使用Flume与Kafka来进行日志数据的采集与传输；实时日志数据通过Spark Streaming进行处理与分析并将分析结果持久化至HBase中；最后采用前后端分离的模式，整个MySQL中的业务数据完成分析结果的可视化展示。总体架构如图3.1所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559366982662/0)

#### 3.2.2 日志数据产生模块架构设计

网站页面浏览日志是存储在服务器上access.log文件里面的结构化数据，并且这些浏览日志数据的结构是可配置的。本文设计使用Python脚本完成页面浏览日志的产生，并结合Linux的crontab功能完成周期性的调用。页面行为日志使用前端埋点的方式产生，在用户产生某种行为时调用js代码，向后端发出请求。后端使用SpringBoot搭建服务，通过解析请求和log4j产生行为日志数据。该模块架构如图3.3所示，产生的日志数据会被下游模块采集。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559367132763/0)

#### 3.2.3 日志采集与传输模块架构设计

上一个模块已经产生了两类日志数据，本模块使用Flume和Kafka完成日志数据的采集与传输。页面浏览日志分布式的存储在网站服务器的access.log文件中，在每个服务器上需要部署一个Flume Agent完成access.log新增日志数据的采集，并将这些日志数据对接至下游Kafka生产者；页面行为日志是由服务器产生的log4j日志数据，通过配置会将这些数据发送至指定地址上的特定端口，在该地址上的服务器部署了Flume Agent，其会监听特定端口来采集日志数据。该Flume Agent会根据日志数据的内容进行分流，对接不同的Kafka生产者，使得不同的行为日志数据以不同的方法被实时处理。消息队列Kafka将Flume采集来的数据通过使用不同的topic传输至下游Spark Streaming。该模块架构如图3.4所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559367178577/0)

#### 3.2.4 日志处理分析和持久化模块架构设计

本模块使用Spark Streaming进行日志数据的处理与分析，针对不同的日志向Spark集群提交相应的Spark Streaming应用，这些应用会根据topic拉取Kafka消息队列中的消息，之后进行处理与分析，分析结果将持久化至HBase中。该模块架构如图3.5所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559367225228/0)

#### 3.2.5 分析结果可视化模块架构设计

分析结果可视化模块采用react作为前端框架，并结合蚂蚁金服技术体验部的UI组件库ant design、ant design Pro以及商业场景下数据可视化解决方案Bizcharts完成数据的多维度展示。后端使用SpringBoot连接HBase，将日志分析结果透出至前端。此外由于网站具体业务的复杂性，前一阶段的分析结果是比较抽象的，还需要结合存有具体业务数据的结构化数据库MySQL来实现更细化的分析结果数据展示，本次设计音乐网站中较重要的结构化数据是歌曲信息的数据。该模块架构如图3.6所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559367274523/0)

### 3.3 本章小结

本章首先对要设计的系统进行总体概括，并分模块进行了需求分析，然后给出了总体架构设计和详细的分模块架构设计。根据各模块的需求给出了可行且合适的解决方案，为下一章系统的具体实现提供了清晰的方向。

## 4设计与实现

### 4.1日志数据产生与采集模块

#### 4.1.1日志数据产生与采集模块设计

由于时间和成本等现实原因，本文设计不能采用真实线上的日志数据，而是采用模拟的方式产生。模拟方式产生的日志数据与真实线上日志完全相同，首先保证浏览日志的数据格式如下：

```java
98.29.167.156 2019-05-10 14:31:01 "GET /music/145.html HTTP/1.1"  200 http://www.baidu.com/s?wd=新近发布  
```

包括请求源地址、时间、方法、请求页面、HTTP协议、请求状态以及来源网站。所以需要设计脚本完成access.log的生成，为了防止日志文件过大，需要定时清理。对于页面行为日志，采用前端埋点的方式产生。首先根据产品设计的需求找到需要记录的用户行为，比如点击、滑动、停留、键入内容等，细化到音乐网站上，用户的行为可以是点击播放音乐、评论音乐、收藏音乐和切换音乐等。这些日志需要在产生这些行为时调用相关后端接口，并向后端传送打点的内容，由后端服务器来完成日志的产生。后端服务器通过log4j产生格式化的日志供下游采集。

这两类日志的采集都使用Flume完成，但是应用不同配置的Flume agent来完成采集。首先两种agent都最重要将日志数据传送给kafka消息队列，但是应为不同的topic，此外对于不同的页面行为日志也要是不同的topic。对于浏览日志，Flume agent使用avro source读取access.log中新增的日志信息，并通过kafka sink传至下游。对于行为日志统一使用一个Flume agent进行采集，使用avro source监听特定的端口，由后端服务器配置log4j发送至指定端口。此外还需要Flume对所有因为日志按照行为的种类进行分流，使下游产生不同topic的消息。

#### 4.1.2日志数据产生与采集模块实现

* 页面浏览日志产生

access.log使用python脚本产生，使用linux的crontab生成定时任务，每个一分钟产生一次access.log。关键代码如下：

```java
f = open("/home/hadoop/data/project/logs/access.log","w+")  
    while count >= 1:   
        query_log = "{ip}\t{local_time}\t\"GET /{url} HTTP/1.1\"\t{status_code}\t{referer}".format(url=sample_url(), ip=sample_ip(), referer=sample_referer(), status_code=sample_status_code(),local_time=time_str)  
        f.write(query_log + "\n")  
        count = count - 1   
```

最终access.log产生的数据结构如下：

```java
187.156.143.55  2019-05-10 15:48:01 "GET /music/128.html HTTP/1.1"  404 -  
87.187.167.124  2019-05-10 15:48:01 "GET /music/128.html HTTP/1.1"  500 -  
156.29.187.168  2019-05-10 15:48:01 "GET /music/112.html HTTP/1.1"  200 http://www.baidu.com/s?wd=新近发布  
```

* 页面行为日志产生

前端埋点的方式由于技术选型的不同有很多方式，本文设计使用的是前后端分离的开发模式，所以在前端要记录行为操作数据的部分编写相应的js代码，调用后端业务接口。后端服务器处理接口调用请求的同时使用log4j产生日志数据。具体实现如下：

前端埋点js代码：

```java
//播放歌曲记录  
    songplay(songInfo){  
        axios.get(Utils.defaultURIdefaultURI+"/actionLogger", { 
            params:{  
                K_topic: 'minions_songplay',//行为种类  
                songId: songInfo.songID//该行为日志需要记录的信息  
            }  
        }).then(function (response) {  
            if (response.data === 1){  
                message.success('添加歌曲播放记录成功');  
                console.log("日志记录成功")  
            } else console.log("日志记录错误")  
        }).catch(function (error) {  
            console.log(error)  
        })  
    }  
```

后端接口日志产生：

```java
@RestController  
@EnableAutoConfiguration  
public class ActionLogController {  
  
    Logger logger = Logger.getLogger(ActionLogController.class.getName());  
    @GetMapping("actionLogger")  
    private int actionLogger(HttpServletRequest request) throws UnsupportedEncodingException {  
        int res = 0;  
        String k_topic = request.getParameter("K_topic");  
        String songID = request.getParameter("songId");  
        try {  
            logger.info("topic:" + k_topic + " songID:" + songID);  
            res = 1;  
        }catch (Exception e){  
            logger.error("error:" + e);  
            e.printStackTrace();  
            res = 0;  
        }  
        return res;  
    }  
}  
```

由于需要与Flume对接，log4j的配置如下：

```java
log4j.appender.flume = org.apache.flume.clients.log4jappender.Log4jAppender  
log4j.appender.flume.Hostname = hadoop000  
log4j.appender.flume.Port = 41415  
log4j.appender.flume.UnsafeMode = true  
log4j.appender.flume.layout=org.apache.log4j.PatternLayout  
log4j.appender.flume.layout.ConversionPattern= %d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c] [%p] - %m%n  
```

这样后端产生log4j日志则会向运行着Flume机器的41415端口发送。最终发送的日志数据格式如下：

```java
2019-05-10 16:23:40,743 [http-nio-8080-exec-6] [com.chaoyue.minions.controller.ActionLogController] [INFO] - topic:minions_songplay songID:17  
```

* Flume配置方案

采集页面浏览日志的Flume agent使用命令从access.log文件中读取新增加的浏览日志数据。注意需要agent的channel选择memory-channel，需要将缓存的容量（capacity，默认值为100）设置一个较大的值，否则当日志数据过多时，会出现异常。本文设计memory-channel.capacity设置为10000。此外使用kafka-sink，将日志分发到指定topic的kafka消息队列中，本文设计页面浏览日志将被分发至topic为streamingtopic的消息队列中。关键配置项如下：

```java
exec-memory-kafka.sources.exec-source.type = exec  
exec-memory-kafka.sources.exec-source.command = tail -F /home/hadoop/data/project/logs/access.log  
  
exec-memory-kafka.channels.memory-channel.capacity = 10000  
exec-memory-kafka.channels.memory-channel.transactionCapacity = 10000 
  
exec-memory-kafka.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink  
exec-memory-kafka.sinks.kafka-sink.topic = streamingtopic  
```

页面行为大类的日志使用同一的Flume agent采集，使用avro-source监听特定端口发来的日志消息数据。为了能够将不同的行为分流，需要使用过滤器interceptors将日志数据中用于分流的标记提取出来，在交给kafka-sink分发至不同topic的消息队列中。interceptor使用正则表达式将日志数据中的标记提取出来命名为topic，在kafka-sink的配置中使用topic即可。interceptor配置如下：

```java
#定义source中的过滤器  
agent1.sources.avro-source.interceptors=i1  
agent1.sources.avro-source.interceptors.i1.type=regex_extractor  
agent1.sources.avro-source.interceptors.i1.regex=topic:(.*?) songID:(.*?)  
agent1.sources.avro-source.interceptors.i1.serializers=s1 s2   
agent1.sources.avro-source.interceptors.i1.serializers.s1.name=topic  
  
agent1.sinks.kafka-sink.type=org.apache.flume.sink.kafka.KafkaSink  
agent1.sinks.kafka-sink.topic = %{topic}  
```

### 4.2日志处理、分析与持久化模块

#### 4.2.1日志处理、分析与持久化模块设计

使用Spark Streaming和HBase完成日志数据的处理、分析和持久化。首先不同类型的日志需要用不同的Spark Streaming应用作业来处理，因为不同的日志分析的逻辑要求是不同的（如处理时间的要求），结构也是不同的，持久化的策略也是不同的。为了便于程序的可靠运行，减少不同类型日志之间处理逻辑的耦合情况，需要将日志处理分析的逻辑按照kafka topic进行划分。日志实时流数据可以根据日志数据结构的字段进行过滤，因为日志数据是结构化的，也是可以定制的。使用HBase进行持久化，一方面支持大量数据的存储，同时还能够动态的修改表结构，增加需要记录的字段十分方便。HBase的表根据时间、类目等主要维度进行设计。

#### 4.2.2日志处理、分析与持久化模块实现

* 应用初始化与流数据获取

每个Spark Streaming应用作业与一个Kafka topic相对应。创建Spark Streaming处理上下文，指定实时流处理的时间间隔，并从Kafka消息队列中消费对应topic的日志数据，如下：

```java
val Array(zkQuorum, group, topics, numThreads) = args  
val sparkConf = new SparkConf().setAppName("MyStreamingApp").setMaster("local[5]")  
val ssc = new StreamingContext(sparkConf, Seconds(60))  
val topicMap = topics.split(",").map((_, numThreads.toInt)).toMap  
val messages = KafkaUtils.createStream(ssc, zkQuorum, group, topicMap)  
```

* 日志数据模板化与清洗

以上就根据日志分析的需求得到了实时的日志流数据，接下来就是对这些日志数据进行必要的过滤。首先对于浏览日志需要将所有不成功的请求清洗掉，保留成功的请求日志。由于浏览日志的数据结构是定制化的，所以我们可以根据特定字段进行清洗，本文设计首先要将每一条数据转换成Scala的模板类对象，后续对每一条数据的操作就是针对这个模板类对象完成的。其次还要将请求状态不成功的日志数据过滤掉。方法如下：

```java
val logs = messages.map(_._2)  
val cleanData = logs.map(line => {  
  val infos = line.split("\t")//把每行日志根据\t分隔符分割  
  val url = infos(2).split(" ")(1)  
  // url为/music/128.html  
  var itemID = 0  
  if (url.startsWith("/music")){//music开头的把编号拿出来  
    val itemIdHTML = url.split("/")(2)  
    itemID = itemIdHTML.substring(0, itemIdHTML.lastIndexOf(".")).toInt  
  }  
  //将每一条日志数据转换成Scala中的模板类，并将请求状态为200的日志数据保留  
  ClickLog(infos(0), DateUtils.parseToMinute(infos(1)), itemID, infos(3).toInt, infos(4))  
}).filter( clicklog => clicklog.itemId != 0 && clicklog.statusCode == 200)
```

最终得到的结构化数据为：

```java
ClickLog(87.30.187.55,20190219090601,145,200,https://www.sogou.com/web?query=今日专辑)  
```

从左到右分别表示请求源地址、时间、请求页面编号、请求状态、来源网站。

* 实时统计访问量

进行业务功能开发时需要兼顾到持久化和后续可视化的可行性。对于访问量的统计需要考虑维度的选择，因为本文设计需要以天为维度和以类目维度记录访问量，即今日到当前时间为止的各类目的访问量和所有页面的访问总量，所以HBase中关于访问量的表中rowkey设计要以日期和类目编号为维度，本文设计访问量表中的rowkey形如20190511_146，列族记录到当前时间为止的访问量。方法如下：

```java
cleanData.map(x => {  
      (x.time.substring(0,8)+"_"+x.itemId, 1)//记录日期与类目编号  
    }).reduceByKey(_+_).foreachRDD(rdd => {  
      rdd.foreachPartition(partitionRecords => {  
        val list = new ListBuffer[itemClickCount]  
        partitionRecords.foreach(pair => {  
          list.append(itemClickCount(pair._1, pair._2))  
        })  
        //将实时统计得到的类目访问量写入HBase  
        itemClickCountDAO.save(list)  
      })  
    })  
```

HBase中行数据的更新操作使用incrementColumnValue方法完成，这样可以直接将当前时间段得到的访问总量直接与之前统计的访问总量相加，方法如下：

```java
def save(list: ListBuffer[itemClickCount]):Unit = {  
    //单例模式获取对表操作的实例  
    val table = HBaseUtils.getInstance().getTable(tableName)  
    for (ele <- list){//每一行的统计信息进行写入  
     table.incrementColumnValue(Bytes.toBytes(ele.day_item),  
        Bytes.toBytes(cf),  
        Bytes.toBytes(qualifer),  
        ele.click_count)  
    }  
  }  
```

以上就完成了在日期和类目编号维度下的实时访问量统计，HBase中记录的数据结构如下：

```java
20190511_130              column=info:click_count, timestamp=1557551220479, 
value=\x00\x00\x00\x00\x00\x00\x0B\xDF 
20190511_131              column=info:click_count, timestamp=1557551220479, 
value=\x00\x00\x00\x00\x00\x00\x11\x0D          
20190511_145              column=info:click_count, timestamp=1557551220483, 
value=\x00\x00\x00\x00\x00\x00-\xFD
20190511_146              column=info:click_count, timestamp=1557551220481, 
value=\x00\x00\x00\x00\x00\x009\x9C  
```

* 实时统计窗口时间内访问量

以上完成了实时统计访问量的需求，但是使用者还需要从更细化的维度观察使用数据。对于访问量，还需要在一天内以24小时为维度进行统计，也就是展示访问量在一天之内的趋势。可以预测网站在一天内的不同时间访问量是不同的，在某个时段可能有高峰，某个时段可能是低谷，这样网站是运营人员就可以选择在不同的时间完成不同是任务，比如在高峰时加大某项业务的投放力度，在访问比较少时完成新版本的发布等。本文设计使用Spark Streaming的window操作接口，分析窗口时间内所有流数据，完成每30分钟窗口期内类目访问量的统计。window的时间间隔和滑动时间均可自定义。实现方法如下：

```java
cleanData.map(x => {  
      (x.time.substring(0,8)+"_"+x.courseId, 1)  
    }).reduceByKeyAndWindow((x: Int, y: Int) => x + y,  
      Seconds(1800), Seconds(1800)).foreachRDD(rdd => {//设置窗口大小为1800秒（30分钟）、滑动时间间隔为30分钟  
      rdd.foreachPartition(partitionRecords => {  
        val list = new ListBuffer[ClickCountTrend]  
        val time = FastDateFormat.getInstance("HHmm").format(new Date())//窗口时间戳  
        partitionRecords.foreach(pair => {  
          list.append(ClickCountTrend(  
            pair._1.substring(0,8)+time+pair._1.substring(8), 
            pair._2))  
        })  
        ClickCountTrendDAO.save(list)  
      })  
    })  
```

以上就实现了每30分钟统计一次各类目在前30分钟内的访问量，对这个数据的积分即是当前时间的总访问量。HBase中持久化的rowkey结构为201905111337_146。这样就能够详细的观察访问量在一天中趋势，以及每个类目访问的趋势。但是目前这个实现方案有一个问题就是，当30分钟窗口时间跨越0点时，会丢失前一天的数据。比如，窗口期为5月10日23：45至11日0：15，由于rowkey设计的原因前15分钟的数据会计算到10日0：15的数据上。为了解决这个问题可以让应用任务在整点时启动。但是解决方案还是需要寻找的。

访问量趋势的可视化展示参见可视化实现部分。

* 来源网站实时统计

针对从搜索引擎来的流量，需要统计来源网站和搜索关键词，这样网站可以进行SEO（搜索引擎优化），提高网站竞争力。对于来源网站的统计依然需要从清洗处理过后的模板数据中完成，这个维度的表中的rowkey以日期、来源网站和类目编号为维度。对于搜索关键词的统计也是以日期为维度，统计当前日期的热门搜索词。

* 页面行为日志分析

对于页面上各种不同的行为日志数据，为了便于后续的处理分析，都需要先将数据格式化为模板类对象。之后再进行日志数据的分析和持久化。以音乐播放为例，模板化方法如下：

```java
  //messages为kafka产生的消息数据  
  val logs = messages.map(_._2)  
  val songPlayLog = logs.map(line => {  
  	val infos = line.split(" ")  
  	val time = DateUtils.parseToMinute(infos(0)+" "+infos(1))  
  	val UID = infos(1).split(":")(1).dropRight(1)  
  	val songID = infos.last.split(":")(1).dropRight(1)  
  	SongPlayLog(time, UID, songID) //最终音乐播放行为模板类结构SongPlayLog(20190401125947, 00001, 15)  
})  
```

对于不同行为日志数据的分析需要根据实际业务需求进行个性化设计。同时还要注意使用实时流处理时要避免复杂的连接操作，实时流处理可以选择记录维度较简单、在原始数据上进行简单抽象的数据结果，后续复杂的分析和连接操作可以放在对实时性要求不高的流程中完成。一下以音乐播放行为为例，进行两个维度的数据分析与持久化。首先统计至当前时间的音乐播放量，方法如下：

```java
songPlayLog.map(x => {  
    	  //rowkey设计为日期与歌曲编号维度  
      (x.time.substring(0,8) + "_" + x.songID, 1)  
    }).reduceByKey(_+_).foreachRDD(rdd => {  
      rdd.foreachPartition(partitionRecords => {  
        val list = new ListBuffer[SongPlayDailyCount]  
        partitionRecords.foreach(pair => {  
          list.append(SongPlayDailyCount(pair._1, pair._2))  
        })  
        SongPlayDailyCountDAO.save(list)  
      })  
    })  
```

同样地对于音乐播放是会有实时热点产生，所以需要实时统计窗口时间内的歌曲播放数据，并在较短时间内更新，以响应热点。首先需要进行HBase表设计，本文设计对于时间段内歌曲播放这类热点数据只保留最新时间的分析结果即可。其次，本文设计针对近一个小时内的歌曲播放进行统计，滑动间隔为10分钟，即每十分钟更新一次近一个小时的歌曲播放数据。在进行HBase表创建时需要设置表中数据的存活时间TTL，本文设计设置为600s（10分钟），即表中数据产生十分钟后失效，最新的数据计算后会被写入表中。分析数据方法如下：

```java
songPlayLog.map(x => {  
      (x.time.substring(0,8) + "_" + x.songID, 1)  
    }).reduceByKeyAndWindow((x: Int, y: Int) => x + y,  
      Seconds(3600), Seconds(600)).foreachRDD(rdd => {  
      rdd.foreachPartition(partitionRecords => {  
        val list = new ListBuffer[RecentlySongPlayCount]  
        partitionRecords.foreach(pair => {  
          list.append(RecentlySongPlayCount(pair._1,  
            pair._2))  
        })  
        RecentlyPlaySongDAO.save(list)  
      })  
    })  
```

以上即得到了实时的歌曲播放统计数据，以及时间段内热点歌曲的播放统计。其他的行为日志的分析需要根据实际需求来实现，本文设计还有歌曲收藏和歌曲评论两个行为日志分析，分别从其他不同的角度反映网站内容数据。

#### 4.2.3 Spark Streaming应用提交集群机器执行

将编写好的Spark Streaming作业程序编写好后使用mvn编译，将编译好的jar包拷贝至集群的机器上，在使用spark-submit指令进行任务的提交，指令如下：

```java
spark-submit --master local[5] \  
--jars $(echo /home/hadoop/app/hbase-1.2.0-cdh5.7.0/lib/*.jar | tr ' ' ',') \ 
--class com.chaoyue.spark.project.scala.MyStreamingApp \  
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0 \  
/home/hadoop/lib/sparktrain-1.0.jar \  
hadoop000:2181 test streamingtopic 1  
```

此时Spark Streaming作业即运行在集群上了，可以再控制台查看运行日志如下：

```java
19/05/12 16:33:00 INFO ShuffledDStream: Time 1557649980000 ms is invalid as zeroTime is 1557649260000 ms , slideDuration is 1800000 ms and difference is 720000 ms  
19/05/12 16:33:00 INFO JobScheduler: Starting job streaming job 1557649980000 ms.0 from job set of time 1557649980000 ms  
19/05/12 16:33:00 INFO SparkContext: Starting job: foreachPartition at MyStreamingApp.scala:59  
19/05/12 16:33:00 INFO JobScheduler: Added jobs for time 1557649980000 ms  
19/05/12 16:33:00 INFO DAGScheduler: Registering RDD 115 (map at MyStreamingApp.scala:56)  
19/05/12 16:33:00 INFO MemoryStore: Block broadcast_43 stored   
19/05/12 16:33:00 INFO BlockManagerInfo: Added broadcast_43_piece0 in memory on 192.168.1.108:42189 (size: 1869.0 B, free: 366.1 MB)  
...  
19/05/12 16:33:05 INFO BlockGenerator: Pushed block input-0-1557649985600  
19/05/12 16:33:08 INFO MemoryStore: Block input-0-1557649988600 stored as bytes in memory (estimated size 305.0 B, free 365.9 MB)  
19/05/12 16:33:08 INFO BlockManagerInfo: Added input-0-1557649988600 in memory on 192.168.1.108:42189 (size: 305.0 B, free: 366.0 MB)  
19/05/12 16:33:08 WARN RandomBlockReplicationPolicy: Expecting 1 replicas with only 0 peer/s.  
19/05/12 16:33:08 WARN BlockManager: Block input-0-1557649988600 replicated to only 0 peer(s) instead of 1 peers  
19/05/12 16:33:08 INFO BlockGenerator: Pushed block input-0-1557649988600  
```

### 4.3分析结果可视化模块

#### 4.3.1分析结果可视化模块设计

可视化部分采用前后端分离的开发模式，最终通过web网页的方式展示可视化结果。可视化模块不只是数据库中数据的展示，还要完成在实时流处理场景中不适合实时处理的其他维度的操作，即完成上个模块没有完成的复杂维度的分析。例如实时流处理中记录的歌曲播放信息只包括时间戳、用户ID和歌曲ID，但是歌曲本身的信息是丰富的，要展示的维度也是丰富的，所以在可视化模块进行复杂的连接操作，完成最终更丰富的分析结果展示。

前端使用react为基础框架，react组件的思想非常适合复用，对于不同数据进行相同类型的展示时可以更换数据源复用组件。此外使用蚂蚁金服的开源react UI组件库ant-design和ant-design Pro，以及商业场景下的数据可视化解决方案Bizcharts，完成丰富的数据可视化展示。后端使用SpringBoot透出相关接口，给前端组件提供需要展示的数据，此外后端还负责实时流处理过程中没有完成的连接操作和其他处理数据的操作。

#### 4.3.2分析结果可视化模块实现

* 可视化web界面概览

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559368878922/0)

如4.1所示，今日数据概览页面展示当日实时数据，包括：实时PageView、七日PV走势、今日PV走势，各类目实时访问统计、各类目访问趋势、今日来源网站统计和今日热搜词云。页面根据维度逐渐细化来布局，从上到下从左到右，首先是PV的总量，然后从一天中的时间为维度和类目为维度细化PV值，再到其他杂项指标。今日PV走势组件展示了网站访问在一天内的趋势，每30分钟统计30分钟内访问量，可以看出音乐网站的用户群体在中午和傍晚到晚上十分活跃。类目实时访问统计展示了用户对哪一个子类目更偏爱，哪些类目需要优化。七日PV比较给一周内的PV比较，可以大致看到某一天的网站浏览状况，需要历史日期更细化的数据可以到历史数据查询里查看。类目访问趋势展示了各类目的今日访问趋势，是前面两个维度的横向和纵向细化，帮助网站运营人员从细节查看网站数据。杂项数据包括来源网站统计和热搜词云，这些展示的是从搜索引擎过来的流量的分析，根据搜索引擎的热搜词，可以帮助网站进行SEO（搜索引擎优化），提高网站排名。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559368954251/0)

如图4.2所示，历史数据查询提供选择日期的组件，可以查看所选日期当天的相关数据。提供30天PV比较，可以看到更长时间区间内网站浏览量的变化，通过这些变化的观察可以对网站的运营策略进行适当的调整。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369327550/0)

图4.3是音乐网站行为的模拟，具有典型的歌曲资源位组件，可以模拟的行为有歌曲播放、收藏和评论。点击按钮则会调用后端相关接口，后端通过log4j记录下行为日志数据。一个歌曲资源包括歌名、歌手、专辑、封面地址、音乐资源地址、风格和标签等，使用MySQL保存这些资源信息。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369422528/0)

图4.4是页面行为日志数据的实时处理分析展示页面。展示的内容包括，今日歌曲播放一览、近一小时最热播放歌曲、播放歌曲类型统计、今日加权热门歌曲、今日歌曲评论和收藏统计。今日歌曲播放一览展示今日实时的歌曲播放量统计。近一小时最热播放歌曲展示一小时窗口时间的歌曲播放量排行，是前一个统计维度的子集。歌曲播放类型统计使用南丁格尔玫瑰花环展示实时歌曲播放不同类型数量的统计，这个维度即是对实时流处理中得到的分析结果进行更高一级的抽象，在可视化的后端将歌曲播放量与歌曲资源进行连接操作得到歌曲类型统计。今日加权热门歌曲通过将三中行为的统计量进行加权，得到热门歌曲，加权权值为播放0.5：收藏1：评论0.8。另外两个展示的是实时歌曲收藏量与评论量。

* 可视化部分详细展示

下面将上节涉及的主要功能组件进行展示。

* 页面实时浏览量
        
实时展示网站今日总浏览量，周同比展示今日与前一周当日的浏览量百分比，日环比展示与前一日的浏览量百分比，日均展示一周七天的浏览量均值。如图4.5所示。
        
![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369535329/0)

* 七日浏览量走势

展示七天前至当日的浏览量走势，如图4.6所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369649506/0)

* 今日实时浏览量走势

以半小时为维度展示今日浏览量数据，每隔半小时统计半小时窗口期中的浏览量，能够清晰展示浏览量在一天中的变化。如图4.7所示，可以看出在凌晨时分访问量逐渐减少，到了早上访问量开始逐渐上升。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369685204/0)

* 各类目实时浏览量统计

以网站不同类目页面为维度进行浏览量数据以及占比的实时展示，音乐网站下设6个大类目：瞩目艺人、最近播放、今日专辑、为你推荐、今日歌单和新近发布。如图4.8所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369719484/0)

* 今日各类目访问趋势

以时间与类目为维度进行浏览量数据的展示，是前两个数据展示组件的细化。如图4.9所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369747929/0)

* 来源网站实时统计

展示今日来源网站的数据统计，如从百度搜索进入主站则将百度搜索记录下来。如图4.10所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369788944/0)

* 今日热搜词云

从搜索引擎进入主站时所进行的搜索词数据统计，如在搜索引擎中搜索“欧美音乐”进入主站，则对“欧美音乐”这个词条进行一次记录，用于搜索引擎优化。如图4.11所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369815950/0)

* 月度浏览量比较

以月为维度对浏览量数据进行展示比较。如图4.12所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369855984/0)

* 页面行为模拟模块

音乐网站页面用户行为的模拟组件，可模拟音乐播放、收藏、评论三种行为，在用户进行页面行为时则会进行日志的记录。如图4.13所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369965341/0)

* 今日歌曲播放一览

今日实时歌曲播放量实时数据展示，如图4.14所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559369987974/0)

* 近一小时播放Top歌曲

近一小时窗口时间内歌曲播放量展示，每十分钟进行一小时窗口时间播放量的统计，展示当下实时热播歌曲。如图4.15所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559370044063/0)

* 播放音乐类型统计

以音乐风格为维度进行歌曲播放量数据实时展示，如图4.16所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559370078824/0)

* 多权重今日热门歌曲

从播放、收藏和评论三个维度进行不同权重的加权计算，得出今日热门歌曲进行展示，可以设计更复杂的加权算法得到热门歌曲，便于运营人员推送歌曲内容。如图4.17所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559370164164/0)

* 今日歌曲收藏一览

今日实时歌曲收藏量数据展示，如图4.18所示。

![](https://puui.qpic.cn/fans_admin/0/3_1359855289_1559370195372/0)

* 今日歌曲评论一览

今日实时歌曲评论量数据展示，如图4.19所示。

![](https://puui.qpic.cn/fans_admin/0/3_1599777765_1559370221681/0)

### 4.4 本章小结

本章在系统需求分析与架构设计的基础上，分模块进行了详细的设计与实现，给出了每个部分的核心代码与解释，并在可视化模块介绍了各个分析数据的展现形式以及其中的意义。

## 5总结与展望

### 5.1 总结

人类可以基于各种信息进行判断、决策，最终创造出价值，而数据作为信息的载体，正在被大量产生。然而数据的形式是多样的，而且是分散的，如何将分布的数据聚合并分析提取出有价值的信息，是大数据时代的挑战。海量数据分析作为互联网行业的闭环模式中的一环，其价值和重要性已经被广泛关注，越来越多的企业可是设立数据部门。一个网站的各个环节都会产生日志数据，这些数据形式多样且分散在不同机器实体上，如何高效采集、聚合、处理、分析和持久化给大数据分析带来了挑战。而现如今，更多的大数据分析需求还要加上“实时处理”这个需求。使用Spark作为分布式数据处理工具，整合海量日志采集工具Flume、可靠消息队列Kafka和海量数据存储HBase，满足了网站日志实时流处理的需求。Flume可定制化的从多种数据源分部式地采集日志，同时具备一定的数据处理能力，可以向多种下游数据目的地传输日志数据。消息队列Kafka保证了海量的日志数据能够有序、不丢失的被消费，同时运用topic机制使得在上游聚合的日志数据以不同的策略被处理和分析。Spark Streaming是整个系统的核心，其是在Spark Core之上的高级API，为整个数据处理流程提供了实时流处理的上下文。Spark的计算模型核心是弹性分布式数据集RDD，提供个基于内存的集群计算的容错性抽象。Spark Streaming的实时流处理能力是基于DStream，其是RDD的一个在时间和空间上的高层抽象，使得我们可以像处理RDD一样处理DStream。同时Spark Streaming支持从多种数据源创建DStream。可以说，Spark Streaming提供了基础实时流处理的解决方案。HBase提供了基于列的海量数据存储，不同于关系型数据库，HBase在大数据处理分析数据持久化方面有许多优点。本文基于Spark Streaming的音乐网站实时流处理，使用上述提及的几个工具和解决方案，同时使用react和SpringBoot进行了分析数据的可视化展示。

本设计完成的主要成果如下：

1）使用Flume实时采集服务器上access.log中的网页浏览日志和使用前端埋点采集到的页面行为日志。解决了分布式日志采集和聚合的问题，同时对日志进行了分流以供下游Kafka使用。

2）使用消息队列Kafka解决大量日志数据传输问题，并为下游数据处理分析Spark Streaming提供实时日志数据源。使用Spark Streaming进行多种类型日志数据的实时处理分析，达到秒级处理和阶段数据处理的能力。在实时流处理中分析出基础业务指标数据，以供上层最这些数据进行其他复杂的操作，并将这些分析结果数据持久化至HBase中。

3）使用React和SpringBoot构建前后端分离项目，同时在后端结合MySQL完成数据分析上层复杂的连接操作。最终从今日数据概览、历史数据查询和页面行为分析模拟三方面进行展示。分析数据展示包括：实时网站访问量、当日实时访问量走势、周和月维度访问量比较、各类目实时访问量及走势、来源网站统计、搜索引擎热搜词统计、歌曲实时播放量、近一小时热门歌曲播放量、歌曲播放类型比较、加权热门歌曲、实时歌曲收藏和评论统计。

本文系统提供的数据可以帮助网站的管理、运营和开发维护人员更好地优化网站，比如优化网站类目内容、优化搜索引擎结果以及选择最合适的时间升级网站服务等。

### 5.2 展望

实时流处理不仅仅是进行简单的数据分析，还有许多实时响应的场景下有更复杂的需求。比如，电商搜索推荐系统在搜索导购的各个场景都需要能够秒级召回个性化推荐数据，而这需要大量的离线和实时数据处理相结合和完成。Spark作为分布式数据处理框架，不仅提供了秒级实时流处理的高级抽象Spark Streaming，还提供了用于机器学习的MLlib、图处理的GraphX、交互式查询SparkSQL，对这些高级抽象进行整合应用可以处理更复杂场景下的需求。