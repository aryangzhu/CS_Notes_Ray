- [1.概述](#1概述)
- [2.xml的用途](#2xml的用途)
  - [1.配置文件](#1配置文件)
  - [2.程序间数据的传输](#2程序间数据的传输)
  - [3.充当小型数据库](#3充当小型数据库)
- [3.技术架构](#3技术架构)
- [4.xml语法](#4xml语法)
  - [文档声明](#文档声明)
  - [元素](#元素)
    - [元素中需要值得注意的地方](#元素中需要值得注意的地方)
  - [属性](#属性)
  - [转义字符](#转义字符)
  - [处理指令](#处理指令)
  - [JDK中的xml API](#jdk中的xml-api)
  - [什么是xml解析](#什么是xml解析)
    - [xml解析操作](#xml解析操作)
      - [1.dom(document object model)文档对象模型](#1domdocument-object-model文档对象模型)
      - [2.sax(simple api for xml)](#2saxsimple-api-for-xml)
- [5.DOM解析操作](#5dom解析操作)
  - [在DOM解析中有几个核心的操作接口](#在dom解析中有几个核心的操作接口)
    - [1.Document](#1document)
    - [2.Node](#2node)
    - [3.NodeList](#3nodelist)
    - [4.NameNodeMap](#4namenodemap)
- [6.SAX解析](#6sax解析)
- [6.DOM和SAX解析的区别](#6dom和sax解析的区别)
- [7.dom4j](#7dom4j)
  - [1.获取](#1获取)
  - [2.获取Document对象](#2获取document对象)
- [8.XPATH](#8xpath)
# 1.概述
xml:extensiable markup language被称作**可扩展性标记语言**
xml简单的历史介绍：
gml->sgml->html->xml
gml(通用标记语言)-在不同的机器上进行通信的数据规范
sgml（标准通用标记语言）
html（超文本标记语言）

1.我们没有xml这种语言之前，我们使用的是String作为两个程序之间的通讯，但是String不擅长关系型结构的数据，描述起来会有歧义
2.html语言本身就有缺陷,例如标记固定，没有真正实现国际化

xml就很好的解决了这些问题

# 2.xml的用途

## 1.配置文件

例如tomcat的web.xml和server.xml,xml能够清楚地描述出程序之间的关系。

## 2.程序间数据的传输

xml的格式是通用的，能够减少交换数据时的复杂性。

## 3.充当小型数据库

如果我们有时候需要人工配置的，那么xml充当小型数据库是个不错的选择，程序直接读取xml文件显然要比读取数据库要快。

# 3.技术架构

xml数据或者xml文档只用于**组织、存储数据**，除此之外的数据生成、读取、传送等等操作都与xml本身无关。
所以，想要操作xml，就需要用到xml之外的技术了：
为xml定规则：现在一般使用DTD或者Schema技术。
解析xml的数据:一般使用DOM或者SAX技术，各有各的优点。
提供样式：xml一般用来存储数据，但设计者的野心很大，也想用来显示数据，就有了xslt（extensiable stylesheet language transformation）可扩展性样式语言。

# 4.xml语法

## 文档声明

xml声明放在xml的第一行
version---版本
encoding--编码
standalone--独立使用--默认是no。standalone表示该xml是不是独立的，如果是yes，则表示这个xml文档是独立的，不能引用外部DTD规范文件；如果是no，则该xml文档不是独立的，表示可以引用外部的DTD规范文档。
正确的文档声明格式，属性的位置不能改变。

```xml
<?xml version="1.0" encoding="utf-8" standalone="no"?>
```

## 元素
首先在这里说明一个概念：在xml中元素和标签指的不是同一个东西，不要被不同的名称迷惑了。

### 元素中需要值得注意的地方

xml元素中的出现的空格和换行都会被当做元素内容进行处理。

每个xml文档中必须有且只有一个根元素

元素必须闭合

大小写敏感

不能交叉嵌套

不能以数字开头

## 属性　

```routeros
<中国 name="china"></中国>
```

注释
CDATA

在编写xml文件时，有些内容可能不想让解析引擎解析执行，而是当做原始内容处理。遇到这种情况就可以使用CDATA区

```xml
<![CDATA[
...内容
]]>
```



## 转义字符
![image.png](https://segmentfault.com/img/bVcMXmk)
## 处理指令
PI（processing instruction)。处理指令用来指挥解析引擎如何解析xml文档内容。
例如：

在xml文档中可以使用xml-stylesheet指令，通知xml解析引擎，应用css文件显示xml文档内容。

```xml
<?xml-stylesheet type="text/css" href="1.css"?>
```

## JDK中的xml API

1.JAXP:主要负责解析xml
2.JAXB:主要负责将xml映射为Java对象



## 什么是xml解析

xml用于组织、存储数据，除此之外的数据生成、读取、传送等等的操作都与xml本身无关。

### xml解析操作

#### 1.dom(document object model)文档对象模型

是W3C组织推荐解析xml的一种方式

#### 2.sax(simple api for xml)

它是xml社区的标准，几乎所有的xml解析器都支持它。

![选区_099.png](https://segmentfault.com/img/bVcM0dc)
应用程序不是直接对xml文档进行操作的，而是由xml解析器对xml文档进行分析，然后应用程序通过对xml解析器所提供的dom接口或者sax接口对分析结果进行操作，从而间接地实现了对xml文档的访问！
![选区_100.png](https://segmentfault.com/img/bVcM0dU)

# 5.DOM解析操作

DOM解析是一个基于对象的API，**它把xml的内容加载到内存中**，生成与xml文档

内容对应的模型！当解析完成，内存中会生成与xml文档的结构与之对应的DOM对

象树，这样就能够根据树的结构，以节点的形式对文档进行操作。

DOM解析会把xml文档加载到内存中，生成DOM树的元素都是以对象的形式存在

的，我们操作这些对象就能够操作xml文档了！

![选区_101.png](https://segmentfault.com/img/bVcM0e1)
１．位于一个节点之上的节点是该节点的父节点（parent）

２．一个节点之下的节点是该节点的子节点（children）

３．同一个层次，具有相同父节点的节点是兄弟节点（sibling）

４．父、祖父节点及所有位于节点上面的，都是节点的祖先（ancestor)

## 在DOM解析中有几个核心的操作接口

### 1.Document

代表整个xml文档，通过Document节点可以访问xml文件中所有的元素内容

### 2.Node

Node节点几乎在xml操作接口中几乎相当于普通Java类的Object,很多核心接口都实现了它，在下面的关系图可以看出。

### 3.NodeList

代表一个节点的集合，通常是一个节点中节点的集合！

### 4.NameNodeMap

表示一组节点和其唯一名称对应的一一对应关系，主要用于属性节点的表示

# 6.SAX解析

SAX采用的是一种顺序的模式进行访问，是一种快速读取xml数据的方式。当时候sax解析器进行操作时，会触发一系列事件SAX。采用事件处理的方式解析XML文件，利用SAX解析xml文档，涉及两个部分：解析器和事件处理器。
sax是一种推式的机制，你创建一个sax解析器，解析器在发现xml文档中的内容就告诉你。如何处理，由程序员决定。

![选区_102.png](https://segmentfault.com/img/bVcM0pK)

# 6.DOM和SAX解析的区别

dom--内存中dom树，如果文档过大，导致溢出。
sax--部分读取，可以处理大文件，只能对文件按顺序从头到尾解析一遍，不支持增删该查。

# 7.dom4j

母的就是为了克服dom和sax缺点

## 1.获取

```java
//获取到解析器
SAXReader saxReader = new SAXReader();

//获取到XML文件的流对象
InputStream inputStream = DOM4j.class.getClassLoader().getResourceAsStream("1.xml");

//通过解析器读取XML文件
Document document = saxReader.read(inputStream);
```

## 2.获取Document对象

```java
１．读取xml文件，获得document对象
SAXReader reader = new SAXReader()；
Document document = reader.read(new File("input.xml"));
```

------

```java
２．解析xml形式的文本，得到document对象
String text = "<members></members>";
Document document=DocumentHelper.parseText(text);
```

------

```java
３．主动创建document对象
Document document =DocumentHelper.createDocument();

//创建根节点
Element root = document.addElement("members");
```

# 8.XPATH

可以帮助我们更加方便地获得xml的节点。

