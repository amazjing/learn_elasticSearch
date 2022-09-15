# 学习Elasticsearch

## 1. Elasticsearch 概述

### 1.1 Elasticsearch 是什么

![image-20220915212813843](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915212813843.png)

The Elastic Stack, 包括 Elasticsearch、Kibana、Beats 和 Logstash（也称为 ELK Stack）。能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化。Elaticsearch，简称为 ES，ES 是一个**开源的高扩展的分布式全文搜索引擎，**是整个 Elastic Stack 技术栈的核心。它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器，处理 PB 级别的数据。



### 1.2 全文搜索引擎

Google，百度类的网站搜索，它们都是根据网页中的关键字生成索引，我们在搜索的时候输入关键字，它们会将该关键字即索引匹配到的所有网页返回；还有常见的项目中应用日志的搜索等等。对于这些非结构化的数据文本，关系型数据库搜索不是能很好的支持。

一般传统数据库，全文检索都实现的很鸡肋，因为一般也没人用数据库存文本字段。进行全文检索需要扫描整个表，如果数据量大的话即使对 SQL 的语法优化，也收效甚微。建立了索引，但是维护起来也很麻烦，对于 insert 和 update 操作都会重新构建索引。

基于以上原因可以分析得出，在一些生产环境中，使用常规的搜索方式，性能是非常差的：

- 搜索的数据对象是大量的非结构化的文本数据。
- 文件记录量达到数十万或数百万个甚至更多。
- 支持大量基于交互式文本的查询。
- 需求非常灵活的全文搜索查询。
- 对高度相关的搜索结果的有特殊需求，但是没有可用的关系数据库可以满足。
- 对不同记录类型、非文本数据操作或安全事务处理的需求相对较少的情况。为了解决结构化数据搜索和非结构化数据搜索性能问题，我们就需要专业，健壮，强大的全文搜索引擎

这里说到的全文搜索引擎指的是目前广泛应用的主流搜索引擎。它的工作原理是计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方式。这个过程类似于通过字典中的检索字表查字的过程。



### 1.3 Elasticsearch And Solr

Lucene 是 Apache 软件基金会 Jakarta 项目组的一个子项目，提供了一个简单却强大的应用程式接口，能够做全文索引和搜寻。在 Java 开发环境里 Lucene 是一个成熟的免费开源工具。就其本身而言，Lucene 是当前以及最近几年最受欢迎的免费 Java 信息检索程序库。但 Lucene 只是一个提供全文搜索功能类库的核心工具包，而真正使用它还需要一个完善的服务框架搭建起来进行应用。

目前市面上流行的搜索引擎软件，主流的就两款：**Elasticsearch** 和 **Solr**,这两款都是基于 Lucene 搭建的，可以独立部署启动的搜索引擎服务软件。由于内核相同，所以两者除了服务器安装、部署、管理、集群以外，对于数据的操作 修改、添加、保存、查询等等都十分类似。

在使用过程中，一般都会将 Elasticsearch 和 Solr 这两个软件对比，然后进行选型。这两个搜索引擎都是流行的，先进的的开源搜索引擎。它们都是围绕核心底层搜索库 - Lucene构建的 - 但它们又是不同的。像所有东西一样，每个都有其优点和缺点：

![image-20220915213125897](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915213125897.png)



### 1.4 Elasticsearch Or Solr

Elasticsearch 和 Solr 都是开源搜索引擎，那么我们在使用时该如何选择呢？

- Google 搜索趋势结果表明，与 Solr 相比，Elasticsearch 具有很大的吸引力，但这并不意味着 Apache Solr 已经死亡。虽然有些人可能不这么认为，但 Solr 仍然是最受欢迎的搜索引擎之一，拥有强大的社区和开源支持。
- 与 Solr 相比，Elasticsearch 易于安装且非常轻巧。此外，你可以在几分钟内安装并运行Elasticsearch。但是，如果 Elasticsearch 管理不当，这种易于部署和使用可能会成为一个问题。基于 JSON 的配置很简单，但如果要为文件中的每个配置指定注释，那么它不适合您。总的来说，如果你的应用使用的是 JSON，那么 Elasticsearch 是一个更好的选择。否则，请使用 Solr，因为它的 schema.xml 和 solrconfig.xml 都有很好的文档记录。
- Solr 拥有更大，更成熟的用户，开发者和贡献者社区。ES 虽拥有的规模较小但活跃的用户社区以及不断增长的贡献者社区。Solr 贡献者和提交者来自许多不同的组织，而 Elasticsearch 提交者来自单个公司。
- Solr 更成熟，但 ES 增长迅速，更稳定。
- Solr 是一个非常有据可查的产品，具有清晰的示例和 API 用例场景。 Elasticsearch 的 文档组织良好，但它缺乏好的示例和清晰的配置说明。

**那么，到底是 Solr 还是 Elasticsearch？**

有时很难找到明确的答案。无论您选择 Solr 还是 Elasticsearch，首先需要了解正确的用例和未来需求。总结他们的每个属性。

- 由于易于使用，Elasticsearch 在新开发者中更受欢迎。一个下载和一个命令就可以启动一切。
- 如果除了搜索文本之外还需要它来处理分析查询，Elasticsearch 是更好的选择
- 如果需要分布式索引，则需要选择 Elasticsearch。对于需要良好可伸缩性和以及性能分布式环境，Elasticsearch 是更好的选择。
- Elasticsearch 在开源日志管理用例中占据主导地位，许多组织在 Elasticsearch 中索引它们的日志以使其可搜索。
- 如果你喜欢监控和指标，那么请使用 Elasticsearch，因为相对于 Solr，Elasticsearch 暴露了更多的关键指标



### 1.5 Elasticsearch 应用案例

- GitHub: 2013 年初，抛弃了 Solr，采取 Elasticsearch 来做 PB 级的搜索。“GitHub 使用Elasticsearch 搜索 20TB 的数据，包括 13 亿文件和 1300 亿行代码”。
- **维基百科**：启动以 Elasticsearch 为基础的核心搜索架构
- **SoundCloud**：“SoundCloud 使用 Elasticsearch 为 1.8 亿用户提供即时而精准的音乐搜索服务”。
- **百度**：目前广泛使用 Elasticsearch 作为文本数据分析，采集百度所有服务器上的各类指标数据及用户自定义数据，通过对各种数据进行多维分析展示，辅助定位分析实例异常或业务层面异常。目前覆盖百度内部 20 多个业务线（包括云分析、网盟、预测、文库、直达号、钱包、风控等），单集群最大 100 台机器，200 个 ES 节点，每天导入 30TB+数据。
- **新浪**：使用 Elasticsearch 分析处理 32 亿条实时日志。
- **阿里**：使用 Elasticsearch 构建日志采集和分析体系。
- **Stack Overflow**：解决 Bug 问题的网站，全英文，编程人员交流的网站。



## 2. Elasticsearch 入门

### 2.1 Elasticsearch 安装

#### 2.1.1 下载软件

Elasticsearch 的官方地址：https://www.elastic.co/cn/

Elasticsearch 最新的版本是 8.4.1（截止 2022.9.15），我们选择 7.8.0 版本

下载地址：https://www.elastic.co/cn/downloads/past-releases#elasticsearch

![image-20220915214101025](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915214101025.png)

Elasticsearch 分为 Linux 和 Windows 版本，基于我们主要学习的是 Elasticsearch 的 Java客户端的使用，所以课程中使用的是安装较为简便的 Windows 版本。 



#### 2.1.2 安装软件

Windows 版的 Elasticsearch 的安装很简单，解压即安装完毕，解压后的 Elasticsearch 的目录结构如下

![image-20220915214320205](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915214320205.png)

文件说明

![image-20220915214430166](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915214430166.png)

解压后，进入 bin 文件目录，点击 elasticsearch.bat 文件启动 ES 服务

![image-20220915214602942](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915214602942.png)

**注意：9300 端口为 Elasticsearch 集群间组件的通信端口，9200 端口为浏览器访问的 http协议 RESTful 端口。**

**测试：**

输入地址：http://localhost:9200，测试结果

![image-20220915214709920](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915214709920.png)



#### 2.1.3 问题解决

- Elasticsearch 是使用 java 开发的，且 7.8 版本的 ES 需要 JDK 版本 1.8 以上，默认安装包带有 jdk 环境，如果系统配置 JAVA_HOME，那么使用系统默认的 JDK，如果没有配置使用自带的 JDK，一般建议使用系统配置的 JDK。 

- 双击启动窗口闪退，通过路径访问追踪错误，如果是“空间不足”，请修改config/jvm.options 配置文件

```properties
# 设置 JVM 初始内存为 1G。此值可以设置与-Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存
# Xms represents the initial size of total heap space
# 设置 JVM 最大可用内存为 1G
# Xmx represents the maximum size of total heap space
-Xms1g
-Xmx1g
```



### 2.2 Elasticsearch 基本操作

#### 2.2.1 RESTful

REST 指的是一组架构约束条件和原则。满足这些约束条件和原则的应用程序或设计就是 RESTful。Web 应用程序最重要的 REST 原则是，客户端和服务器之间的交互在请求之间是无状态的。从客户端到服务器的每个请求都必须包含理解请求所必需的信息。如果服务器在请求之间的任何时间点重启，客户端不会得到通知。此外，无状态请求可以由任何可用服务器回答，这十分适合云计算之类的环境。客户端可以缓存数据以改进性能。

在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI (Universal Resource Identifier) 得到一个唯一的地址。所有资源都共享统一的接口，以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、PUT、POST 和DELETE。 

在 REST 样式的 Web 服务中，每个资源都有一个地址。资源本身都是方法调用的目标，方法列表对所有资源都是一样的。这些方法都是标准方法，包括 HTTP GET、POST、PUT、DELETE，还可能包括 HEAD 和 OPTIONS。简单的理解就是，如果想要访问互联网上的资源，就必须向资源所在的服务器发出请求，请求体中必须包含资源的网络路径，以及对资源进行的操作(增删改查)。



#### 2.2.2 客户端安装

如果直接通过浏览器向 Elasticsearch 服务器发请求，那么需要在发送的请求中包含HTTP 标准的方法，而 HTTP 的大部分特性且仅支持 GET 和 POST 方法。所以为了能方便地进行客户端的访问，可以使用 Postman 软件



#### 2.2.3 数据格式

Elasticsearch 是面向文档型数据库，一条数据在这里就是一个文档。为了方便大家理解，我们将 Elasticsearch 里存储文档数据和关系型数据库 MySQL 存储数据的概念进行一个类比

![image-20220915215056937](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915215056937.png)

ES 里的 Index 可以看做一个库，而 Types 相当于表，Documents 则相当于表的行。这里 Types 的概念已经被逐渐弱化，Elasticsearch 6.X 中，一个 index 下已经只能包含一个type，Elasticsearch 7.X 中, Type 的概念已经被删除了。

6 用 JSON 作为文档序列化的格式，比如一条用户信息：

```json
{
 "name" : "John",
 "sex" : "Male",
 "age" : 25,
 "birthDate": "1990/05/01",
 "about" : "I love to go rock climbing",
 "interests": [ "sports", "music" ]
}
```



#### 2.2.4 HTTP 操作

##### 2.2.4.1 索引操作

1. **创建索引**

对比关系型数据库，创建索引就等同于创建数据库

在 Postman 中，向 ES 服务器发 **PUT** 请求 ：http://127.0.0.1:9200/shopping

![image-20220915225153388](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915225153388.png)

```json
{
 "acknowledged"【响应结果】: true, # true 操作成功
 "shards_acknowledged"【分片结果】: true, # 分片操作成功
 "index"【索引名称】: "shopping"
}
# 注意：创建索引库的分片数默认 1 片，在 7.0.0 之前的 Elasticsearch 版本中，默认 5 片
```

如果重复添加索引，会返回错误信息

![image-20220915225344683](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915225344683.png)



2. **查看所有索引**

   在 Postman 中，向 ES 服务器发 **GET** 请求 ：http://127.0.0.1:9200/_cat/indices?v

   这里请求路径中的_cat 表示查看的意思，indices 表示索引，所以整体含义就是查看当前 ES服务器中的所有索引，就好像 MySQL 中的 show tables 的感觉，服务器响应结果如下

   ![image-20220915225549460](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915225549460.png)

   ![image-20220915225619640](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915225619640.png)



3. **查看单个索引**

   向 ES 服务器发 **GET** 请求 ：http://127.0.0.1:9200/shopping

   ![image-20220915225956239](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915225956239.png)

查看索引向 ES 服务器发送的请求路径和创建索引是一致的。但是 HTTP 方法不一致。这里可以体会一下 RESTful 的意义，

```json
{
 	"shopping"【索引名】: { 
 		"aliases"【别名】: {},
 		"mappings"【映射】: {},
 		"settings"【设置】: {
 			"index"【设置 - 索引】: {
 				"creation_date"【设置 - 索引 - 创建时间】: "1614265373911",
 				"number_of_shards"【设置 - 索引 - 主分片数量】: "1",
 				"number_of_replicas"【设置 - 索引 - 副分片数量】: "1",
 				"uuid"【设置 - 索引 - 唯一标识】: "eI5wemRERTumxGCc1bAk2A",
 				"version"【设置 - 索引 - 版本】: {
 					"created": "7080099"
 				},
				 "provided_name"【设置 - 索引 - 名称】: "shopping"
 			}
		 }
	 } 
}
```



4. **删除索引**

   向 ES 服务器发 **DELETE** 请求 ：http://127.0.0.1:9200/shopping

   ![image-20220915230251994](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915230251994.png)

   重新访问索引时，服务器返回响应：**索引不存在**

   ![image-20220915230339447](http://typora-imagelist.oss-cn-qingdao.aliyuncs.com/image-20220915230339447.png)



##### 2.2.4.2 文档操作

