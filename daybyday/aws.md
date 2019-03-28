# AWS - Amazon Web Services

AWS 在中国（北京）区域和其他地区是隔离的。

## AWS Products & Services

* [Kinesis](https://github.com/liyp/0o0_books/tree/a0c14fcca258dce040adae2c489930ca71dea410/daybyday/aws.amazon.com/cn/kinesis/README.md)
* ...

DOC

* ALL SERVICES DOC [http://docs.amazonaws.cn/aws/latest/userguide/services.html](http://docs.amazonaws.cn/aws/latest/userguide/services.html)
* 区域和终端节点 [http://docs.amazonaws.cn/general/latest/gr/rande.html](http://docs.amazonaws.cn/general/latest/gr/rande.html)

### Amazon Kinesis

Amazon Kinesis is a fully managed, cloud-based service for real-time data processing over large, distributed data streams. Amazon Kinesis can continuously capture and store terabytes of data per hour from hundreds of thousands of sources such as website clickstreams, financial transactions, social media feeds, IT logs, and location-tracking events.

> Amazon Kinesis是实时处理数据流的一个管道。类似于Apache Storm，但是完全由亚马逊Web服务托管和扩展。
>
> Kinesis所做的这种类型的处理与其他亚马逊服务所能做的处理一样。你已经使用了亚马逊的简单队列服务、简单通知服务和自动扩展容量到处理实时数据流（实际上，我的公司也在这么做）。 亚马逊Kinesis真正的优势在于能够更轻松地从头开始构建新的服务，为整个流程提供完整的管理服务。

**问：Amazon Kinesis 与 Amazon SOS 有何区别？**

Amazon Kinesis 允许实时处理大数据流。它提供了记录排序，以及以使用与多个 Amazon Kinesis 应用程序相同的顺序读取和/或重播数据记录的能力。Amazon Kinesis 客户端库 \(KCL\) 能够将给定分区键的所有记录提供给同一记录处理器，从而可支持更加轻松地构建从同一 Amazon Kinesis 数据流读取数据的多个应用程序（例如，执行计数、累积和筛选）。

Amazon Simple Queue Service \(Amazon SQS\) 提供了可靠且高度可扩展的托管队列，用于存储在计算机之间传输的消息。Amazon SQS 让您能够在分布式应用程序组件之间轻松移动数据，并帮助构建可以单独处理消息的应用程序（带消息级确认/失败语义），如自动工作流。

**问：我何时应使用 Amazon Kinesis，何时应使用 Amazon SQS？**

我们建议将 Amazon Kinesis 用于具有类似以下要求的使用案例：

* 将相关记录路由到同一记录处理器（如同流处理 MapReduce）。例如，当给定键的所有记录被传输到同一记录处理器时，计数和累积更加简单。
* 记录的排序。例如，您希望将日志数据从应用程序主机传输到处理/归档主机，同时保持日志报表的顺序。
* 多个应用并发使用同一数据流的能力。例如，您拥有的一个应用程序会更新实时控制面板，另一个应用程序会归档数据到 Amazon Redshift。您希望这两个应用程序并行独立地使用同一数据流的数据。
* 在数小时后以同一顺序使用记录的能力。例如，您拥有一个记账应用程序，同时审计应用程序在开单应用程序数小时后运行。由于 Amazon Kinesis 存储数据最多 24 个小时，因此您可在记账应用程序后运行后的最长 24 小时内运行审计应用程序。

我们建议将 Amazon SQS 用于具有类似以下要求的使用案例：

* 消息收发语义（如消息级别确认/失败）和可见性超时。例如，您拥有一个工作项目队列，同时希望单独跟踪每个项目的成功完成情况。Amazon SQS 跟踪确认/失败，因此应用程序无需保留持续的检查点/光标。在配置的可见性超时后，Amazon SQS 将删除已确认消息并重新提交失败的消息。
* 单个消息延迟。例如，您拥有一个作业队列，同时需要计划带延迟的单个作业。通过 Amazon SQS，您可为单个消息配置为具有最多 15 分钟的延迟。
* 动态增加读取时间的并发性/吞吐量。例如，您拥有一个工作队列，同时希望添加更多读者，直到储备清除。通过 Amazon Kinesis，您可以扩展到足够数量的分片（但请注意，您需要提前配置足够的分片）。
* 充分利用 Amazon SQS 的透明扩展功能。例如，由于业务的偶尔负载尖峰或自然增长，您将缓存请求和负载变化。由于每个缓存的请求可以单独处理，因此 Amazon SQS 可以透明扩展以处理负载，无需提供任何配置说明。

#### KPL and KCL

```markup
<dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>amazon-kinesis-client</artifactId>
    <version>1.6.0</version>
</dependency>
```

#### sdk API

* [aws-sdk-java](https://github.com/aws/aws-sdk-java)
* [http://docs.aws.amazon.com/zh\_cn/AWSSdkDocsJava/latest/DeveloperGuide/](http://docs.aws.amazon.com/zh_cn/AWSSdkDocsJava/latest/DeveloperGuide/)
* [https://docs.amazonaws.cn/kinesis/latest/dev/introduction.html](https://docs.amazonaws.cn/kinesis/latest/dev/introduction.html)

```markup
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.amazonaws</groupId>
            <artifactId>aws-java-sdk-bom</artifactId>
            <version>1.10.11</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-kinesis</artifactId>
    </dependency>
</dependencies>
```

